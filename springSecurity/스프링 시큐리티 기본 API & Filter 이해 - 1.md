### **스프링 시큐리티 기본 API 및  Filter를 이해한다**

---

* **Intellij + Spring Boot + Spring Security를 활용하여 프로젝트를 구성한다.**
* **스프링 시큐리티 기본 API 및  Filter를 이해한다**
	1. [프로젝트 구성](#프로젝트-구성)
	2. [인증 API - 사용자 정의 보안 기능 구현](#인증-API---사용자-정의-보안-기능-구현)
	3. [Form Login 인증](#Form-Login-인증)

---

1. ### 프로젝트 구성

	* **순서**

		1. **intellij 의 file -> new -> project -> Spring Initializr 클릭 후 자바 버전 1.8 이상 설정**

		2. **`spring-boot-starter-web` 의존성을 자동 추가 후 프로젝트 생성**

		3. **테스트를 위한 `SecurityController` 구현**

			```java
			package io.security.basicsecurity;
			
			import org.springframework.web.bind.annotation.GetMapping;
			import org.springframework.web.bind.annotation.RestController;
			
			@RestController
			public class SecurityController {
			    @GetMapping("/")
			    public String index(){
			        return "home";
			    }
			}
			```

	* **프로젝트 확인**

		1. **localhost:8080**접속 시 만든적 없는 로그인 창이 뜬다.

			![image](https://user-images.githubusercontent.com/52272332/96541734-957bfa00-12db-11eb-9dfd-82546861b682.png)

		2. 시큐리티가 제대로 동작했다는 증거이다.

		3. 만들지는 않았지만 시큐리티에서 기본적인 아이디 **user를 제공한다**

		4. **비밀번호를 프로젝트 구동시 콘솔창에 찍힌다.**

			![image](https://user-images.githubusercontent.com/52272332/96541806-c1977b00-12db-11eb-9211-fdf6370dfec6.png)

		5. 해당 아이디와 비밀번호를 입력하면 로그인 성공한다.

			![image](https://user-images.githubusercontent.com/52272332/96541865-dc69ef80-12db-11eb-8af3-bf72123afddc.png)

	* **인증 API - 스프링 시큐리티 의존성 추가**

		* **스프링 시큐리티의 의존성 추가 시 일어나는 일들**
			1. **서버 구동시 스프링 시큐리티의 초기화 작업 및 보안 설정이 이루어진다.**
			2. **별도의 설정이나 구현을 하지 않아도 기본적인 웹 보안 기능이 현재 시스템에 연동되어 작동한다.**
		* **대표적인 기능**
			1. 모든 요청은 인증이 되어야 자원에 접근 가능해진다.
			2. 인증 방식은 폼 로그인 방식과 httpBasic 로그인 방식을 제공한다.
			3. 기본 로그인 페이지 제공
			4. 기본 계정은 한개 제공
		* **문제점**
			1. **계정 추가, 권한 추가, DB 연동은 직접해야된다.**
			2. 기본적인 보안 기능 외에 시스템에 필요로 하는 세부적이고 추가적인 보안 기능 필요
			3. **이러한 스프링 시큐리티의 내용을 쉽게 알아보고 직접 구현할 수 있도록 공부하는 것이 내 목표이다.**

2. ### 인증 API - 사용자 정의 보안 기능 구현

	* **기본적인 구성**

		![image](https://user-images.githubusercontent.com/52272332/96546788-d8db6600-12e5-11eb-96fe-1db7f2644430.png)

	* **스프링 시큐리티의 웹 보안 기능 초기화 및 설정**

		1. `WebSecurityConfigurerAdapter`를 상속 받은 `SecurityConfig`을 작성한다

		2. `SecurityConfig`는 사용자가 정의한 보안 설정 클래스이다.

		3. `@Override`를 통해 정의를 진행한다.

		4. `SecurityConfig.java`

			```java
			package io.security.basicsecurity;
			
			import org.springframework.context.annotation.Configuration;
			import org.springframework.security.config.annotation.web.builders.HttpSecurity;
			import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
			import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
			
			@Configuration
			@EnableWebSecurity
			public class SecurityConfig extends WebSecurityConfigurerAdapter {
			
			    @Override
			    protected void configure(HttpSecurity http) throws Exception {
			        http
			                .authorizeRequests() // 인가 설정
			                .anyRequest().authenticated(); // 모든 request에 인가를 받도록 설정
			        http
			                .formLogin(); // form 방식으로 받도록 설정한다.
			    }
			}
			
			```

	* **코드와 어노테이션 설명**

		* **어노테이션**
			1. `@Configuration` : 이를 선언하므로써 **해당 클래스에서 1개 이상의 `Bean`이 생성하고 있음을 명시해준다.** `Bean`으로 등록 후 사용시 필수적으로 적어줘야 한다.
			2. `@EnableWebSecurity` : **스프링 시큐리티의 사용을 선언한다.**
		* **코드**
			1. `http` : http 가 들어왔을 때 어떻게 처리할지를 .method()를 통해 설정한다.
			2. `.authorizeRequests()` : 시큐리티 처리에 HttpServletRequest를 이용한다는 것을 의미
			3. `anyRequest()` : 모든 Reqeust에 대해서
			4. `.authenticated()` : 인가를 받도록 설정
			5. `.formLogin()` : form 방식으로 인증 받도록 설정한다.

	* **사용자 정의 아이디 패스워드 설정**

		* `application.properties`에 추가한다.

			```properties
			spring.security.user.name=user
			spring.security.user.password=123
			```

		* 후 재 실행하면 해당 아이디 비밀번호로 로그인이 가능하다.

3. ### Form Login 인증

	* **Login**

		1. **기본적은 Client-Server 구조에서 로그인과 거의 똑같다**

			![image](https://user-images.githubusercontent.com/52272332/96550096-62d9fd80-12eb-11eb-8414-9159483a8a14.png)

		2. **사용자가 POST 를 통해 인증에 성공하면 세션을 생성**

		3. **인증 토큰을 생성, 저장 후 `SecurityContext`에 저장 후 전달한다**

		4. **다시 /home에 접근시 세션에 저장된 인증 토큰으로 접근 / 인증을 유지한다**

	* **로그인에 대한 추가 설정**

		* **Form 로그인 인증 기능이 작동함**

			`SecurityConfig.java`

			```java
			@Configuration
			@EnableWebSecurity
			public class SecurityConfig extends WebSecurityConfigurerAdapter {
			
			    @Override
			    protected void configure(HttpSecurity http) throws Exception {
			        http
			                .authorizeRequests() // 인가 설정
			                .anyRequest().authenticated(); // 모든 request에 대해 인증를 받도록 설정
			        http    // 인증 정책
			                .formLogin()
			                .loginPage("/loginPage") // 여기는 누구나 접근이 가능하도록 인가 설정해야된다.
			                .defaultSuccessUrl("/")// 성공시 이동될 url
			                .failureUrl("/login") //실패시 이동될 url 
			                .usernameParameter("userId") // form 값이랑 같아야 한다.
			                .passwordParameter("passwd")
			                .loginProcessingUrl("/login_proc")
			                .successHandler(new AuthenticationSuccessHandler() {
			                    // 로그인 성공시 핸들러
			                    @Override
			                    public void onAuthenticationSuccess(HttpServletRequest httpServletRequest, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
			                        // authentication : 인증 성공시 최종 결과
			                        // 구체적인 로직 구현
			                        System.out.println("authentication : " + authentication.getName());
			                        response.sendRedirect("/");
			                    }
			                })
			                .failureHandler(new AuthenticationFailureHandler() {
			                    // 로그인 실패시 핸들러 
			                    @Override
			                    public void onAuthenticationFailure(HttpServletRequest httpServletRequest, HttpServletResponse response, AuthenticationException exception) throws IOException, ServletException {
			                        System.out.println("exception : "+exception.getMessage());
			                        response.sendRedirect("/login");
			                    }
			                })
			                .permitAll();
			    }
			}
			
			```

			`SecurityController.java`

			```java
			 @GetMapping("/loginPage")
			    public String loginPage(){
			        return "loginPage";
			    }
			// 추가해준다.
			```

		* **실행**

			![image](https://user-images.githubusercontent.com/52272332/96551397-30310480-12ed-11eb-99a1-a83650987deb.png)

			* 바로 로그인 페이지로 이동이 확인된다.

	* **성공, 실패 핸들러**

		* **해당 확인을 위해서 `loginPage("/loginPage")`는 주석처리한다.**

		* `.successHandler`
			 1. `System.out.println("authentication : " + authentication.getName());` 으로 확인하면 
			 2. **성공시 : authentication : user**이 콘솔에 찍히고
		* `failureHandler`
			1. **`System.out.println("exception : "+exception.getMessage());`** 으로 확인하면
			2. **실패시 : exception : 자격 증명에 실패하였습니다.** 이 콘솔에 찍힌다. 

	* **시큐리티 설정 username, password 확인**

		* **페이지 소스보기 클릭시 화면의 `form`화면에 대한 코드를 확인할 수 있다.**

			```html
			 <body>
			     <div class="container">
			      <form class="form-signin" method="post" action="/login_proc">
			        <h2 class="form-signin-heading">Please sign in</h2>
			<div class="alert alert-success" role="alert">You have been signed out</div>        <p>
			          <label for="username" class="sr-only">Username</label>
			          <input type="text" id="username" name="userId" class="form-control" placeholder="Username" required autofocus>
			        </p>
			        <p>
			          <label for="password" class="sr-only">Password</label>
			          <input type="password" id="password" name="passwd" class="form-control" placeholder="Password" required>
			        </p>
			<input name="_csrf" type="hidden" value="d7217223-b0d7-4b8e-9e59-c7e8c41a7b50" />
			        <button class="btn btn-lg btn-primary btn-block" type="submit">Sign in</button>
			      </form>
			</div>
			</body>
			```

		* **`body`부분을 가져와서 확인해보면**

			1. `<form class="form-signin" method="post" action="/login_proc">` 에서 우리가 설정한 `login_proc`로 url이 설정된 것을 확인할 수 있다.
			2. 마찬가지로 username, passwd 또한 확인 가능하다.
			3. `<input type="text" id="username" name="userId" class="form-control" placeholder="Username" required autofocus>`
			4. `<input type="password" id="password" name="passwd" class="form-control" placeholder="Password" required>`

	* **정리**

		```java
		http    // 인증 정책
		                .formLogin() // form 방식으로 인증 받도록 설정한다.
		                .loginPage("/loginPage") // 여기는 누구나 접근이 가능하도록 인가 설정해야된다.
		                .defaultSuccessUrl("/")// 성공시 이동될 url
		                .failureUrl("/login") //실패시 이동될 url 
		                .usernameParameter("userId") // form 값이 변경된다
		                .passwordParameter("passwd") //커스텀 form에서는 이를 일치시켜야한다.
		                .loginProcessingUrl("/login_proc") // 여기도 일치시켜야 한다.
		                .successHandler() // 성공시 핸들러
		    			.failureHandler() // 실패시 핸들러 
		    			.permitAll(); // 로그인 페이지는 누구나 접근 가능해야 한다. 이를 설정해준다. 
		```

		

