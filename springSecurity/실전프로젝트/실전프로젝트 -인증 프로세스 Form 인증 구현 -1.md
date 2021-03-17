### 실전프로젝트 -인증 프로세스 Form 인증 구현

---

1. [실전 프로젝트 생성]
2. [정적 자원 관리 - WebIgnore 설정]

---

1. ### 실전 프로젝트 생성

	1. **프로젝트 생성하기**

		* File -> project -> Spring Initializr

	2. **코드 다운로드**

		* https://github.com/onjsdnjs/corespringsecurityfinal
		* **css,js,html 등 정적인 파일은 코드제공이 없으므로 다운받아서 사용한다.**

	3. **코드 추가**

		1. **구조**

			![image](https://user-images.githubusercontent.com/52272332/111250980-b3167b00-8651-11eb-9f1b-176da5a15f49.png)

		2. `ConfigController`

			```java
			@Controller
			public class ConfigController {
			
			    @GetMapping("/config")
			    public String config(){
			        return "admin/config";
			    }
			}
			```

		3. `MessageController`

			```java
			@Controller
			public class MessageController {
			
			    @GetMapping("/messages")
			    public String mypage(){
			        return "user/messages";
			    }
			}
			```

		4. `UserController`

			```java
			@Controller
			public class UserController {
			
			    @GetMapping(value = "/mypage")
			    public String myPage() throws Exception {
			        return "user/mypage";
			    }
			}
			```

		5. `homeController`

			```java
			@Controller
			public class HomeController {
			
			    @GetMapping(value="/")
			    public String home() throws Exception {
			        return "home";
			    }
			}
			```

	4. **`SecurityConfig`**

		* **SpringSecurity 설정파일**

		```java
		
		@Configuration
		@EnableWebSecurity
		public class SecurityConfig extends WebSecurityConfigurerAdapter {
		
		    @Override
		    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
		
		        String password = passwordEncoder().encode("1111");
		        // 패스워드 인코더를 통해 비밀번호 생성 
		
		        auth.inMemoryAuthentication().withUser("user").password(password).roles("USER");
		        auth.inMemoryAuthentication().withUser("manager").password(password).roles("MANAGER");
		        auth.inMemoryAuthentication().withUser("admin").password(password).roles("ADMIN");
		        
		        // ID, PW, Authorization을 설정 
		    }
		
		    @Override
		    protected void configure(HttpSecurity http) throws Exception {
		        http
		                .authorizeRequests()
		                .antMatchers("/").permitAll()
		                .antMatchers("/mypage").hasRole("USER")
		                .antMatchers("/messages").hasRole("MANAGER")
		                .antMatchers("/config").hasRole("ADMIN")
		                .anyRequest()
		                .authenticated() // 시스템 접속시 인증 필요
		        .and()
		                .formLogin()
		                ;
		        // formLogin()방식으로 로그인을 진행하며
		        // antMaters의 url에 접근하려면 해당 role를 가지고 있어야 한다. 
		    }
		
		
		    @Bean
		    public PasswordEncoder passwordEncoder() {
		        //패스워드 인코더 생성
		        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
		    }
		}
		
		```

	5. **실행**

		1. **`/`**에 접근은 아무런 권한이 없어도 가능하다.

			![image](https://user-images.githubusercontent.com/52272332/111250980-b3167b00-8651-11eb-9f1b-176da5a15f49.png)

		2. **`/user`**는 `user`권한이 있어야 가능하다.

			![image](https://user-images.githubusercontent.com/52272332/111250980-b3167b00-8651-11eb-9f1b-176da5a15f49.png)

		3. **`/messages`**접근은 `manager`권한이 있어야 접근 가능하므로 `user`는 접근 불가능

			![image](https://user-images.githubusercontent.com/52272332/111251945-7ba8ce00-8653-11eb-997f-0c33d58f493a.png)

		4. `manager`권한으로 로그인 후 접근

			![image](https://user-images.githubusercontent.com/52272332/111252006-9713d900-8653-11eb-8999-db0684365946.png)

2. ### 정적 자원 관리 - WebIgnore

	1. **`SercurityConfig`**

		```java
		@Configuration
		@EnableWebSecurity
		public class SecurityConfig extends WebSecurityConfigurerAdapter {
		
		    @Override
		    public void configure(WebSecurity web) throws Exception {
		        web.ignoring().requestMatchers(PathRequest.toStaticResources().atCommonLocations());
		        // 정적 파일들은 보안 필터를 거치지 않고 통과된다.
		        // css, js, images 등
		    }
		}
		```

		* **정적인 파일은 보안 필터를 거치지 않고 통과되도록 설정한다**
		* **필터를 거치지 않는것과 필터에서 인가에 성공하는 것은 다르다.**

3. ### 사용자 DB 등록 및 PasswordEncoder

	1. **`package`** 추가하기

		1. **구조**

			![image](https://user-images.githubusercontent.com/52272332/111259638-aa7a7080-8662-11eb-95c9-925a6d4dfa87.png)

		2. `Account`

			```java
			@Entity
			@Data
			public class Account {
			    @Id
			    @GeneratedValue
			    private long id;
			    private String username;
			    private String password;
			    private String email;
			    private String age;
			    private String role;
			}
			```

		3. `AccountDto`

			```java
			@Data
			public class AccountDto {
			    private String username;
			    private String password;
			    private String email;
			    private String age;
			    private String role;
			}
			```

		4. `UserRepository`

			```java
			public interface UserRepository extends JpaRepository<Account, Long> {
			}
			```

		5. `UserService`

			```java
			public interface UserService {
			    void createUser(Account account);
			}
			```

		6. `UserServiceImpl`

			```java
			@Service("userService")
			public class UserServiceImpl implements UserService {
			
			    @Autowired
			    private UserRepository userRepository;
			
			    @Transactional
			    @Override
			    public void createUser(Account account) {
			        userRepository.save(account);
			    }
			}
			```

		7. `UserController`

			```java
			@Controller
			public class UserController {
			
			    @Autowired
			    private UserService userService;
			
			    @Autowired
			    private PasswordEncoder passwordEncoder;
			
			    @GetMapping(value = "/mypage")
			    public String myPage() throws Exception {
			        return "user/mypage";
			    }
			
			    @GetMapping("/users")
			    public String createUser(){
			        return "user/login/register";
			    }
			
			    @PostMapping("/users")
			    public String createUser(AccountDto accountDto){
			
			        ModelMapper modelMapper = new ModelMapper();
			        Account account = modelMapper.map(accountDto, Account.class);
			        account.setPassword(passwordEncoder.encode(account.getPassword()));
			        userService.createUser(account);
			
			        return "redirect:/";
			    }
			
			}
			
			```

			* `accountDto`를 넘겨 받은 뒤 패스워드를 암호화하고 저장
			* `return "redirect:/"`를 통해 루트 페이지로 다시 이동시킨다.

	2. **JPA 설정**

		```properties
		spring.datasource.driver-class-name=com.mysql.jdbc.Driver
		spring.datasource.url=jdbc:mysql://localhost:3306/springsecurity?serverTimezone=UTC&characterEncoding=UTF-8
		spring.datasource.username=your Id
		spring.datasource.password=your Password
		
		spring.jpa.hibernate.ddl-auto=create
		spring.jpa.database-platform=org.hibernate.dialect.MySQL5InnoDBDialect
		spring.jpa.properties.hibernate.format_sql=true
		spring.jpa.properties.hibernate.show_sql=true
		spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
		
		spring.thymeleaf.cache=false
		
		spring.devtools.livereload.enabled=true
		spring.devtools.restart.enabled=true
		
		spring.main.allow-bean-definition-overriding=true
		
		#spring.profiles.active=pointcut
		```

	3. `html`

		1. `register.html`

			```html
			<html  xmlns:th="http://www.thymeleaf.org">
			<head th:replace="layout/header::userHead"></head>
			<body>
			<div th:replace="layout/top::header"></div>
			<body>
			
			<div class="container d-flex justify-content-center">
			  <div style="width:700px; margin-top: 30px;">
			    <form class="form-horizontal" th:action="@{/users}" method="post">
			      <div class="form-group">
			        <label for="username" class="col-sm-2 control-label">아이디</label>
			        <div class="col-sm-10">
			          <input type="text" class="form-control input-large" name="username" id="username" placeholder="username" required>
			        </div>
			      </div>
			
			      <div class="form-group">
			        <label for="password" class="col-sm-2 control-label">비밀번호</label>
			        <div class="col-sm-10">
			          <input type="password" class="form-control input-large" name="password" id="password" placeholder="Password" data-minlength="6" required>
			        </div>
			      </div>
			      <div class="form-group">
			        <label for="email" class="col-sm-2 control-label">이메일</label>
			        <div class="col-sm-10">
			          <input type="email" class="form-control input-large" name="email" id="email" placeholder="이메일" required>
			        </div>
			      </div>
			      <div class="form-group">
			        <label for="age" class="col-sm-2 control-label">나이</label>
			        <div class="col-sm-10">
			          <input type="text" class="form-control input-large" name="age" id="age" placeholder="나이" required>
			        </div>
			      </div>
			      <div class="form-group">
			        <label for="role" class="col-sm-2 control-label">권한</label>
			        <div class="col-sm-10">
			          <input type="text" class="form-control input-large" name="role" id="role" placeholder="권한" required>
			        </div>
			      </div>
			      <div class="form-group">
			        <div class="col-sm-offset-1 col-sm-10">
			          <button type="Submit" class="btn btn-dark btn-lg">가입하기</button>
			        </div>
			      </div>
			    </form>
			  </div>
			</div>
			</body>
			</html>
			```

	4. **실행화면**

		* 회원가입

			![image](https://user-images.githubusercontent.com/52272332/111260004-650a7300-8663-11eb-8e1f-a8b15d5c5cb4.png)

		* **디비 저장 확인**

			![image](https://user-images.githubusercontent.com/52272332/111260063-81a6ab00-8663-11eb-8ef1-b187b7ca8b41.png)