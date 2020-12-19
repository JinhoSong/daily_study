## springboot와 react를 이용한 채팅 서비스

---

1. [프로젝트 생성](#프로젝트-생성)
2. [웹소켓 연결](#웹소켓-연결)
3. [Simple Web Socket Client로 확인하기](#Simple-Web-Socket-Client로-확인하기)

---

1. ### 프로젝트 생성

	1. Create new Project

	2. Spring Intializr 선택

	3. `Gradle`선택과 ID들 지정

		![image](https://user-images.githubusercontent.com/52272332/98328547-54a11680-2039-11eb-91d0-ffe89b728a4b.png)

	4. `implementation` 추가를 위한 설정

		![image](https://user-images.githubusercontent.com/52272332/98328623-82865b00-2039-11eb-8201-c2bdaea5c93b.png)

		```groovy
		plugins {
		    id 'org.springframework.boot' version '2.3.5.RELEASE'
		    id 'io.spring.dependency-management' version '1.0.10.RELEASE'
		    id 'java'
		}
		
		group = 'com.chatting.spring'
		version = '0.0.1-SNAPSHOT'
		sourceCompatibility = '11'
		
		configurations {
		    compileOnly {
		        extendsFrom annotationProcessor
		    }
		}
		
		repositories {
		    mavenCentral()
		}
		
		dependencies {
		    implementation 'org.springframework.boot:spring-boot-starter-web'
		    implementation 'org.springframework.boot:spring-boot-starter-websocket'
		    compileOnly 'org.projectlombok:lombok'
		    annotationProcessor 'org.projectlombok:lombok'
		    testImplementation('org.springframework.boot:spring-boot-starter-test') {
		        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'
		    }
		}
		
		test {
		    useJUnitPlatform()
		}
		```

2. ### 웹소켓 연결

    1. `WebsocketChatHandler` 
    
        ```java
        package com.chatting.spring.demo.handler;
        //import는 생략
        @Slf4j
        @RequiredArgsConstructor
        @Component
        public class WebSocketChatHandler extends TextWebSocketHandler {
        
        private final ObjectMapper objectMapper;
        private final ChatService chatService;
        
        @Override
        protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        String payload = message.getPayload();
        log.info("payload {}", payload);
        //TextMessage textMessage = new TextMessage("Welcome chatting sever~^^");
        //session.sendMessage(textMessage);
        
        ChatMessage chatMessage = objectMapper.readValue(payload,ChatMessage.class);
        ChatRoom chatRoom = chatService.findRoomById(chatMessage.getRoomId());
        chatService.handleActions(session,chatMessage,chatRoom); // 명령어 처리를 위임한다. 
        log.info("chatMessage {}",chatMessage);
        }
        }
        
        ```
    
        * 기본적으로 **서버 - 클라이언트는 1 : N의 관계를 맺습니다.**
        * 한개의 서버에 여러 클라이언트가 접속이 가능합니다.
        * 클라이언트가 발송한 메시지를 받아서 처리하는 `Handler`기능을 수행합니다.
        * 지금여기선 받은 메시지를 `log.info`를 통해 보여줍니다.
        * **받아오는 메시지의 `roomId`값을 이용해 해당 채팅방에 메시지를 전달하도록 합니다.**
    
    2. `WebSocketConfig`
    
        ```java
        package com.chatting.spring.demo.config;
        
        @RequiredArgsConstructor
        @Configuration
        @EnableWebSocket
        public class WebSocketConfig implements WebSocketConfigurer {
        
        private final WebSocketChatHandler webSocketChatHandler;
        
        @Override
        public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(webSocketChatHandler,"/ws/chat").setAllowedOrigins("*");
        }
        }
        
        ```
    
        * `@EnableWebsocket`을 통해 Websocket이 활성화됩니다.
        * `WebSocketConfigurer`의 `registerWebSocketHandlers`를 `@Override`해서 위에서 만든 `WebSocketHandler`를 이용하는 소켓이됩니다.
    
    3. **채팅방과, 채팅메시지 설정**
    
        * `ChatMessage`
        
        ```java
        package com.chatting.spring.demo.chat;
        @Getter
        @Setter
        public class ChatMessage {
        
        private MessageType messageType;
        private String roomId;
        private String sender;
        private String message;
        }
        
        ```
    
        * `MessageType`
        
        ```java
        package com.chatting.spring.demo.chat;
        
        public enum MessageType {
        ENTER, TALK
        }
        ```
    
        * **`MessageType`**은 서버-클라이언트 간에 무슨 메시지를 보낸것인지 구분하기 위해 선언된 `class`입니다.
        * `ENTER`라면 입장을 `TALK`라면 대화를 시도한것이라고 알려줍니다.
        * `ChatRoom`
    
        ```java
        package com.chatting.spring.demo.chat;
        @Getter
        @Slf4j
        public class ChatRoom {
        private String roomId;
        private String name;
        private Set<WebSocketSession> sessions = new HashSet<>();
        
        @Builder
        public ChatRoom(String roomId, String name) {
        this.roomId = roomId;
        this.name = name;
        }
        
        ```
    
        * **`Set<WebSocketSession> sessions`** 정보 리스트를 멤버필드로 가집니다.
        * 해당 변수는 **채팅방에 참여한 `WebSocket`들을 추가하여 채팅방에 입장을 관리합니다.**
        * `JPA로 치면 ManyToMany`로 묶을 것이다.
    
    4. **채팅 프로토콜을 위한`ChatService`**
    
        ```java
        package com.chatting.spring.demo.service;
        
        @Slf4j
        @RequiredArgsConstructor
        @Service
        public class ChatService {
        
        private final ObjectMapper objectMapper;
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
        
        // messageType에 따라 다르게 이용될 프로토콜을 정의한다. ex ENTER -> 입장하기
        public void handleActions(WebSocketSession session, ChatMessage chatMessage, ChatRoom chatRoom) {
        //log.info("dddd",chatMessage);
        MessageType command = chatMessage.getMessageType(); // 커맨드 명령어를 따로 뺀다.
        
        // 수업시간에 작성했던 코드와 똑같도록 명령어 단위로 나눈다.
        // NPE를 피하기위한 equals 순서 
        if (MessageType.ENTER.equals(command)) {
            this.enterRoom(session,chatMessage,chatRoom);
        } else if(MessageType.TALK.equals(command)){
            this.sendMessageAllClient(chatMessage,chatRoom);
        }
        
        //        sendMessage(chatMessage, chatService);
        }
        
        public void enterRoom(WebSocketSession session, ChatMessage chatMessage,ChatRoom chatRoom){
        // ENTER의 경우
            chatRoom.getSessions().add(session); // 채팅에 참가한 모든 세션을 가져온다.
            chatMessage.setMessage(chatMessage.getSender() + "님이 입장했습니다."); // 메시지를 새로 세팅하고
            this.sendMessageAllClient(chatMessage,chatRoom); // 보낸다.
        // 입장했습니다 표시를 보낸다.
        }
        
        public void sendMessageAllClient(ChatMessage chatMessage,ChatRoom chatRoom) {
        //일반적인 TALK
        chatRoom.getSessions().parallelStream().forEach(session -> this.sendMessageForOneClient(session,chatMessage ));
        }
        public <T> void sendMessageForOneClient(WebSocketSession session, ChatMessage chatMessage) {
        try {
            String sendMessage = chatMessage.getSender() + " : "+ chatMessage.getMessage();
            session.sendMessage(new TextMessage(objectMapper.writeValueAsString(sendMessage)));
        } catch (IOException e) {
            log.error(e.getMessage(), e);
               }
           }
        }
        ```
    
        * **채팅에 핵심이 되는 서비스 부분이다.**
        * `MessageType`에 따라 프로토콜에게 어떤 메시지를 반환해줄지 결정된다.
        * **기능은 2가지**
            1. **채팅방에 입장시 알려주는 기능 -> `ENTER`**
            2. **채팅방에 메시지를 입력하여 접속한 클라이언트 모두에게 보여주는 기능 -> `TALK`**
        * `handleActions` 메서드를 통해서 어떤 명령어의 종류를 알아낸다. -> `if else`
        * 이 후 해당 명령에 따른 서비스를 제공하면된다.
        * **해당 구조가 맞다면 추가할 모든 기능들을 여기에 정의할 것이다.**

3. ### Simple Web Socket Client로 확인하기

	1. **post man을 이용하여 roomId생성하기**

		![image](https://user-images.githubusercontent.com/52272332/98338369-6db3c280-204d-11eb-9b1c-62a272c12398.png)

	2. **생성된 roomId로 접속 후 채팅 `ENTER -> TALK`**

		![image](https://user-images.githubusercontent.com/52272332/98338462-920f9f00-204d-11eb-9773-8f2b15226902.png)

		1. 먼저 `ENTER`로 접속 후 `TALK`를 보내야한다.
		2. 로그는 obj 단위로 넘어가지만 리턴받는 메시지는 직접 작성한 형태로 날라온다.
		3. **채팅방 나가기 등 후속 조치들이 많이 필요하다.**