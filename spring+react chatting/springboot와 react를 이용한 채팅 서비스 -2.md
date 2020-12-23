## springboot와 react를 이용한 채팅 서비스

1. [STOMP](#STOMP)
2. [Stomp 실습](#Stomp-실습)
3. [View 설정](#View-설정)

---

1. ### STOMP

	1. **Stomp 란**
		* **Stomp는 메시징 전송을 효율적으로 하기 위해 나온 프로토콜이며 기본적으로 pub/sub 구조로 되어있다.**
		* 개발시 메시지 요청과 받는 부분이 명확하게 구분되어 편리하다.
		* 통신 메시지의 헤더에 값을 세팅할 수 있어 **헤더 값 기반으로 통신시 인증처리를 구현이 가능하다** -> `Security`?
	2. **`pub/sub`**
		* `pub/sub`란 메시지를 공급하는 주체와 소비하는 주체를 분리하여 제공하는 메시징 방법입니다.
		* 기본적인 예시
			1. 우체통 `(topic)`
			2. 집배원 `(publisher)`
			3. 구독자 `(subsciber)` 있다고 가정했을 때
		* **시나리오**
			1. 채팅방을 생성한다 -> `pub/sub` 구현을 위한 하나의 `topic`이 생성된다.
			2. 채팅방을 입장한다 -> `topic`을 `sub`한다.
			3. 채팅방에 메시지를 보낸다. -> `pub` 를 보낸다.
			4. 채팅방에 메시지를 보여준다 -> `topic`에 참여한 사람들에게 `sub`로 메시지를 보낸다.

2. ### Stomp 실습

	1. `gradle` 추가

		```java
		dependencies {
		    implementation 'org.springframework.boot:spring-boot-starter-freemarker'
		    implementation 'org.webjars.bower:bootstrap:4.3.1'
		    implementation 'org.webjars.bower:vue:2.5.16'
		    implementation 'org.webjars.bower:axios:0.17.1'
		    implementation 'org.webjars:sockjs-client:1.1.2'
		    implementation 'org.webjars:stomp-websocket:2.3.3-1'
		    implementation 'com.google.code.gson:gson:2.8.0'
		    compile("org.apache.tomcat.embed:tomcat-embed-jasper")
		
		}
		```

		* client를 구성해줄 `vue.js`에 `sockjs, axios, bootstrap`등 의존성을 추가해줍니다.
		* gradle를 새로고침하면 `static templates` 폴더가 새로 생성됩니다.

	2. `application.properties`

		```properties
		spring.devtools.livereload.enabled=true
		spring.devtools.restart.enabled=true
		spring.freemarker.cache=false
		
		spring.freemarker.template-loader-path=classpath:/templates
		spring.freemarker.suffix=.ftl
		spring.freemarker.contentType=text/html
		spring.freemarker.charset=UTF-8
		
		spring.mvc.view.prefix=classpath:/templates
		```

		* 생성된 폴더로 `controller`가 접근하도록 설정해줍니다.

	3. `WebSocketConfig`

		```java
		package com.chatting.spring.demo.config;
		@RequiredArgsConstructor
		@Configuration
		//@EnableWebSocket
		@EnableWebSocketMessageBroker
		public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {
		
		    @Override
		    public void configureMessageBroker(MessageBrokerRegistry registry) {
		        registry.enableSimpleBroker("/sub"); // 수신 위치
		        registry.setApplicationDestinationPrefixes("/pub"); // 발신 위치
		    }
		
		    @Override
		    public void registerStompEndpoints(StompEndpointRegistry registry) {
		        registry.addEndpoint("/ws-stomp").setAllowedOrigins("*").withSockJS();
		    }
		}
		
		```

		* `Stomp` 사용을 위해 `@EnableWebSocketMessageBroker`로 어노테이션을 교체해줍니다.
		* 상속받는 클래스도 `WebSocketMessageBrokerConfigurer`으로 교체합니다.
		* `pub/sub`을 설정합니다.
			1. `registry.enableSimpleBroker("/sub")` : 메시지 구독 요청 의 `prefix = /sub`
			2. `registry.setApplicationDestinationPrefixes("/pub")` : 메시지 발행 요청의 `prefix = /pub`
		* `registerStompEndpoints`로 url의 뒷부분에 추가될 `endpoint`를 설정합니다.
			1. `registry.addEndpoint("/ws-stomp").setAllowedOrigins("*").withSockJS()`
			2. `ws://localhost:8080/ws-stomp`로 설정됩니다.

	4. `ChatRoomRepository` 생성

		```java
		package com.chatting.spring.demo.Repository;
		
		import com.chatting.spring.demo.chat.ChatRoom;
		import org.springframework.stereotype.Repository;
		
		import javax.annotation.PostConstruct;
		import java.util.*;
		
		@Repository
		public class ChatRoomRepository {
		    private Map<String, ChatRoom> chatRooms;
		
		    @PostConstruct
		    private void init() {
		        chatRooms = new LinkedHashMap<>();
		    }
		
		    public List<ChatRoom> findAllRoom() {
		        return new ArrayList<>(chatRooms.values());
		    }
		
		    public ChatRoom findRoomById(String roomId) {
		        return chatRooms.get(roomId);
		    }
		
		    public ChatRoom createRoom(String name) {
		        String randomId = UUID.randomUUID().toString();
		        ChatRoom chatRoom = ChatRoom.builder()
		                .roomId(randomId)
		                .name(name)
		                .build();
		        chatRooms.put(randomId, chatRoom);
		        return chatRoom;
		    }
		}
		```

		* `ChatService`에 있던 채팅방 설정은 모두 이리로 가져옵니다.

	5. `ChatRoomController`

		```java
		package com.chatting.spring.demo.controller;
		// import 생략...
		
		@RequiredArgsConstructor
		@Controller
		@RequestMapping("/chat")
		public class ChatRoomController {
		
		    private final ChatRoomRepository chatRoomRepository;
		
		    // 채팅 리스트 화면
		    @GetMapping("/room")
		    public String rooms(Model model) {
		        return "/chat/room";
		    }
		    // 모든 채팅방 목록 반환
		    @GetMapping("/rooms")
		    @ResponseBody
		    public List<ChatRoom> room() {
		        return chatRoomRepository.findAllRoom();
		    }
		    // 채팅방 생성
		    @PostMapping("/room")
		    @ResponseBody
		    public ChatRoom createRoom(@RequestParam String name) {
		        return chatRoomRepository.createRoom(name);
		    }
		    // 채팅방 입장 화면
		    @GetMapping("/room/enter/{roomId}")
		    public String roomDetail(Model model, @PathVariable String roomId) {
		        model.addAttribute("roomId", roomId);
		        return "/chat/roomdetail";
		    }
		    // 특정 채팅방 조회
		    @GetMapping("/room/{roomId}")
		    @ResponseBody
		    public ChatRoom roomInfo(@PathVariable String roomId) {
		        return chatRoomRepository.findRoomById(roomId);
		    }
		}
		```

		

	6. `ChatController`

		```java
		package com.chatting.spring.demo.controller;
		
		import java.util.List;
		
		@RequiredArgsConstructor
		@Controller
		public class ChatController {
		
		    private final ChatService chatService;
		
		    @MessageMapping("/chat/message")
		    public void message(ChatMessage chatMessage){
		        chatService.handleActions(chatMessage);
		    }
		}
		```

		* `@MessageMapping`을 통해 Websocket으로 오는 메시지 발행을 처리합니다. 

	7. `ChatService` 

		* **메시지 발행을 위한 publisher 를 구현합니다.**
		* 이전에 사용된 `session, WebSocketHandler`를 삭제합니다.

		```java
		package com.chatting.spring.demo.service;
		
		@Slf4j
		@RequiredArgsConstructor
		@Service
		public class ChatService {
		
		    private final SimpMessageSendingOperations simpMessageSendingOperations; // 앞에 Simp주의 Simple이 아니다
		
		    public void handleActions(ChatMessage chatMessage) {
		
		        MessageType command = chatMessage.getMessageType(); // 커맨드 명령어를 따로 뺀다.
		        //System.out.println(command);
		        // 수업시간에 작성했던 코드와 똑같도록 명령어 단위로 나눈다.
		        if (MessageType.ENTER.equals(command)) {
		            this.enterRoom(chatMessage);
		        } else if(MessageType.TALK.equals(command)){
		            this.SendAllClient(chatMessage);
		        }
		    }
		
		    public void enterRoom(ChatMessage chatMessage){
		        // ENTER의 경우
		        chatMessage.setMessage(chatMessage.getSender() + "님이 입장했습니다."); // 메시지를 새로 세팅하고
		        simpMessageSendingOperations.convertAndSend("/sub/chat/room/"+ chatMessage.getRoomId(), chatMessage);
		        // 입장했습니다 표시를 보낸다.
		    }
		
		    public void SendAllClient(ChatMessage chatMessage){
		        // TALK의 경우 
		        simpMessageSendingOperations.convertAndSend("/sub/chat/room/"+ chatMessage.getRoomId(), chatMessage);
		    }
		}
		```

		* `simpMessageSendingOperations`의 메소드를 통해 간단하게 `sub`한 모두에게 메시지를 전달한다.
		* `@SendTo`를 이용하여 1대1 통신도 가능하다. -> 뒤에 추가할 예정

3. ### View 설정

    1. `room.ftl`

    ```html
    <!doctype html>
      	<html lang="en">
      	<head>
      	    <title>Websocket Chat</title>
      	    <meta charset="utf-8">
      	    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
      	    <!-- CSS -->
      	    <link rel="stylesheet" href="/webjars/bootstrap/4.3.1/dist/css/bootstrap.min.css">
      	    <style>
      	        [v-cloak] {
      	            display: none;
      	        }
      	    </style>
      	</head>
      	<body>
      	<div class="container" id="app" v-cloak>
      	    <div class="row">
      	        <div class="col-md-12">
      	            <h3>채팅방 리스트</h3>
      	        </div>
      	    </div>
      	    <div class="input-group">
      	        <div class="input-group-prepend">
      	            <label class="input-group-text">방제목</label>
      	        </div>
      	        <input type="text" class="form-control" v-model="room_name" @keyup.enter="createRoom">
      	        <div class="input-group-append">
      	            <button class="btn btn-primary" type="button" @click="createRoom">채팅방 개설</button>
      	        </div>
      	    </div>
      	    <ul class="list-group">
      	        <li class="list-group-item list-group-item-action" v-for="item in chatrooms" v-bind:key="item.roomId" v-on:click="enterRoom(item.roomId)">
      	            {{item.name}}
      	        </li>
      	    </ul>
      	</div>
      	<!-- JavaScript -->
      	<script src="/webjars/vue/2.5.16/dist/vue.min.js"></script>
      	<script src="/webjars/axios/0.17.1/dist/axios.min.js"></script>
      	<script src="/webjars/bootstrap/4.3.1/dist/js/bootstrap.min.js"></script>
      	<script src="/webjars/sockjs-client/1.1.2/sockjs.min.js"></script>
      	<script>
      	    var vm = new Vue({
      	        el: '#app',
      	        data: {
      	            room_name : '',
      	            chatrooms: [
      	            ]
      	        },
      	        created() {
      	            this.findAllRoom();
      	        },
      	        methods: {
      	            findAllRoom: function() {
      	                axios.get('/chat/rooms').then(response => { this.chatrooms = response.data; });
      	            },
      	            createRoom: function() {
      	                if("" === this.room_name) {
      	                    alert("방 제목을 입력해 주십시요.");
      	                    return;
      	                } else {
      	                    var params = new URLSearchParams();
      	                    params.append("name",this.room_name);
      	                    axios.post('/chat/room', params)
      	                        .then(
      	                            response => {
      	                                alert(response.data.name+"방 개설에 성공하였습니다.")
      	                                this.room_name = '';
      	                                this.findAllRoom();
      	                            }
      	                        )
      	                        .catch( response => { alert("채팅방 개설에 실패하였습니다."); } );
      	                }
      	            },
      	            enterRoom: function(roomId) {
      	                var sender = prompt('대화명을 입력해 주세요.');
      	                localStorage.setItem('wschat.sender',sender);
      	                localStorage.setItem('wschat.roomId',roomId);
      	                location.href="/chat/room/enter/"+roomId;
      	            }
      	        }
      	    });
      	</script>
      	</body>
      	</html>
    ```
    2. `roomdetail.ftl`
    
    ```html
    <!doctype html>
      	<html lang="en">
      	<head>
      	    <title>Websocket ChatRoom</title>
      	    <!-- Required meta tags -->
      	    <meta charset="utf-8">
      	    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
      	    <!-- Bootstrap CSS -->
      	    <link rel="stylesheet" href="/webjars/bootstrap/4.3.1/dist/css/bootstrap.min.css">
    
      	    <style>
      	        [v-cloak] {
      	            display: none;
      	        }
      	    </style>
      	</head>
      	<body>
      	<div class="container" id="app" v-cloak>
      	    <div>
      	        <h2>{{room.name}}</h2>
      	    </div>
      	    <div class="input-group">
      	        <div class="input-group-prepend">
      	            <label class="input-group-text">내용</label>
      	        </div>
      	        <input type="text" class="form-control" v-model="message" @keyup.enter="sendMessage">
      	        <div class="input-group-append">
      	            <button class="btn btn-primary" type="button" @click="sendMessage">보내기</button>
      	        </div>
      	    </div>
      	    <ul class="list-group">
      	        <li class="list-group-item" v-for="message in messages">
      	            {{message.sender}} - {{message.message}}</a>
      	        </li>
      	    </ul>
      	    <div></div>
      	</div>
      	<!-- JavaScript -->
      	<script src="/webjars/vue/2.5.16/dist/vue.min.js"></script>
      	<script src="/webjars/axios/0.17.1/dist/axios.min.js"></script>
      	<script src="/webjars/bootstrap/4.3.1/dist/js/bootstrap.min.js"></script>
      	<script src="/webjars/sockjs-client/1.1.2/sockjs.min.js"></script>
      	<script src="/webjars/stomp-websocket/2.3.3-1/stomp.min.js"></script>
      	<script>
      	    // websocket & stomp initialize
      	    var sock = new SockJS("/ws-stomp");
      	    var ws = Stomp.over(sock);
      	    // vue.js
      	    var vm = new Vue({
      	        el: '#app',
      	        data: {
      	            roomId: '',
      	            room: {},
      	            sender: '',
      	            message: '',
      	            messages: []
      	        },
      	        created() {
      	            this.roomId = localStorage.getItem('wschat.roomId');
      	            this.sender = localStorage.getItem('wschat.sender');
      	            this.findRoom();
      	        },
      	        methods: {
      	            findRoom: function() {
      	                axios.get('/chat/room/'+this.roomId).then(response => { this.room = response.data; });
      	            },
      	            sendMessage: function() {
      	                if(this.message == ''){
      	                    alert("메시지를 입력하세요")
      	                } else {
      	                    ws.send("/pub/chat/message", {}, JSON.stringify({messageType:'TALK', roomId:this.roomId, sender:this.sender, message:this.message}));
      	                    this.message = '';
      	                }
      	            },
      	            recvMessage: function(recv) {
      	                this.messages.unshift({"messageType":recv.messageType,"sender":recv.messageType=='ENTER'?'[알림]':recv.sender,"message":recv.message})
    
      	            }
      	        }
      	    });
      	    // pub/sub event
      	    ws.connect({}, function(frame) {
      	        ws.subscribe("/sub/chat/room/"+vm.$data.roomId, function(message) {
      	            var recv = JSON.parse(message.body);
      	            vm.recvMessage(recv);
      	        });
      	        ws.send("/pub/chat/message", {}, JSON.stringify({messageType:'ENTER', roomId:vm.$data.roomId, sender:vm.$data.sender}));
      	    }, function(error) {
      	        alert("error "+error);
      	    });
      	</script>
      	</body>
      	</html>
    ```


* **채팅방 입장시 `sw-stomp`로 서버 연결을 한다.**

* **구독하는 액션을 수행한다.**

* **`ws.subscribe("/sub/chat/room/" + vm.$data.roomId, function(message){ Method()})`**

* 구독은 `/sub/chat/room/채팅방 번호`로 구독하고 이 주소를 `topic`으로 이용하여 서버에 메시지를 발행, 수신한다.

* **메시지를 발행`pub`을 하는데 이것을 구독자들은 `ws.subscribe`에서 대기하고 있다가 발송된 메시지를 받아서 처리**

* **화면**

	



