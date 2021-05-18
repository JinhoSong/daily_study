### 실전프로젝트 -인증 프로세스 Form 인증 구현

---

1. [커스텀 로그인 페이지 생성하기](#커스텀-로그인-페이지-생성하기)
2. [로그아웃 및 인증에 따른 화면 보안 처리](#로그아웃-및-인증에-따른-화면-보안-처리)
3. [인증 부가 기능 WebAuthenticationDetails, AuthenticationDetailsSource](#인증-부가-기능-WebAuthenticationDetails,-AuthenticationDetailsSource)

---

1. ### 커스텀 로그인 페이지 생성하기

	1. `SecurityConfig`

		```java
		@Configuration
		@EnableWebSecurity
		public class SecurityConfig extends WebSecurityConfigurerAdapter {
		  
		    @Override
		    protected void configure(HttpSecurity http) throws Exception {
		        http
		                .authorizeRequests()
		                .antMatchers("/", "/users").permitAll()
		                .antMatchers("/mypage").hasRole("USER")
		                .antMatchers("/messages").hasRole("MANAGER")
		                .antMatchers("/config").hasRole("ADMIN")
		                .anyRequest()
		                .authenticated() // 시스템 접속시 인증 필요
		        .and()
		                .formLogin()
		                .loginPage("/login") // 커스텀 페이지를 지정
		                .loginProcessingUrl("/login_proc")
		                .authenticationDetailsSource(authenticationDetailsSource)
		                .defaultSuccessUrl("/")
		                .permitAll()
		        ;
		    }
		}
		
		```

	2. `LoginController`

		```java
		package io.security.coresecurity.lecture.controller.login;
		
		@Controller
		public class LoginController {
		
		    @GetMapping("/login")
		    public String login() {
		        return "user/login/login";
		    }
		}
		```

	3. `login.html`

		```html
		<!DOCTYPE html>
		<html xmlns:th="http://www.thymeleaf.org">
		<head th:replace="layout/header::userHead"></head>
		<script>
		    function formLogin(e) {
		        var username = $("input[name='username']").val().trim();
		        var password = $("input[name='password']").val().trim();
		        var data = {"username" : username, "password" : password};
		        $.ajax({
		            type: "post",
		            url: "/api/login",
		            data: JSON.stringify(data),
		            dataType: "json",
		            beforeSend : function(xhr){
		                // xhr.setRequestHeader(csrfHeader, csrfToken);
		                xhr.setRequestHeader("X-Requested-With", "XMLHttpRequest");
		                xhr.setRequestHeader("Content-type","application/json");
		            },
		            success: function (data) {
		                console.log(data);
		                window.location = '/';
		            },
		            error : function(xhr, status, error) {
		                console.log(error);
		                window.location = '/login?error=true&exception=' + xhr.responseText;
		            }
		        });
		    }
		</script>
		<body>
		<div th:replace="layout/top::header"></div>
		<div class="container text-center">
		    <div class="login-form d-flex justify-content-center">
		        <div class="col-sm-5" style="margin-top: 30px;">
		            <div class="panel">
		                <p>아이디와 비밀번호를 입력해주세요</p>
		            </div>
		            <div th:if="${param.error}" class="form-group">
		                <span th:text="${exception}" class="alert alert-danger">잘못된 아이디나 암호입니다</span>
		            </div>
		            <form th:action="@{/login_proc}" class="form-signin" method="post">
		                <input type="hidden" th:value="secret" name="secret_key" />
		                <div class="form-group">
		                    <input type="text" class="form-control" name="username" placeholder="아이디" required="required" autofocus="autofocus">
		                </div>
		                <div class="form-group">
		                    <input type="password" class="form-control" name="password" placeholder="비밀번호" required="required">
		                </div>
		<!--                <button type="button" onclick="formLogin()" id="formbtn" class="btn btn-lg btn-primary btn-block">Ajax 로그인</button>-->
		                <button type="submit" class="btn btn-lg btn-primary btn-block">로그인</button>
		            </form>
		        </div>
		    </div>
		</div>
		</body>
		</html>
		```

		

2. ### 로그아웃 및 인증에 따른 화면 보안 처리

	1. **로그아웃 방법**

		* `<form>` 태그를 사용해서 POST로 요청을 보낸다.
		* `<a>` 태그를 사용해서 GET으로 요청을 보낸다. -> **SecurityContextLogoutHandler**사용

	2. **로그인/로그아웃 표현**

		```html
		<li class="nav-item" sec:authorize="isAnonymous()"><a class="nav-link text-light"th:href="@{/login}">로그인</a></li>
		<li class="nav-item" sec:authorize="isAnonymous()"><a class="nav-link text-light" h:href="@{/users}">회원가입</a></li>
		<li class="nav-item" sec:authorize="isAuthenticated()"><a class="nav-link text-th:href="@{/logout}">로그아웃</a></li>
		```

		* `sec:authorize="isAnonymous()"` : 익명의 사용자 -> 로그인 하지 않은 사용자
		* `sec:authorize="isAuthenticated()"` : 인증된 사용자 -> 로그인에 성공한 사용자

	3. `LoginController`

		```java
		package io.security.coresecurity.lecture.controller.login;
		
		@Controller
		public class LoginController {
		
		    @GetMapping("/login")
		    public String login() {
		        return "user/login/login";
		    }
		
		    @GetMapping("/logout")
		    public String logout(HttpServletRequest request, HttpServletResponse response) {
		
		        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();
		
		        if (authentication != null) {
		            new SecurityContextLogoutHandler().logout(request, response, authentication);
		        }
		        return "redirect:/login";
		    }
		}
		```

3. ### 인증 부가 기능 WebAuthenticationDetails, AuthenticationDetailsSource

	1. **Form 인증 WebAuthenticationDetails**

		![image](https://user-images.githubusercontent.com/52272332/112121851-0add5500-8c03-11eb-9983-b34d912e8c85.png)

	2. `FormWebAuthenticationDetails`

		```java
		package io.security.coresecurity.lecture.security.common;
		
		public class FormWebAuthenticationDetails extends WebAuthenticationDetails {
		
		    private String secretKey;
		
		    public FormWebAuthenticationDetails(HttpServletRequest request) {
		        super(request);
		        secretKey = request.getParameter("secret_key");
		    }
		
		    public String getSecretKey() {
		        return secretKey;
		    }
		}
		```

	3. `login.html`

		```html
		<form th:action="@{/login_proc}" class="form-signin" method="post">
		    <input type="hidden" th:value="secret" name="secret_key" />
		    <!-- 생략 -->
		    <button type="submit" class="btn btn-lg btn-primary btn-block">로그인</button>
		</form>
		```

		* `hidden` 타입으로 `key : secret_key, value : secret` 을 넘긴다.

	4. `CustomAuthenticationProvider`

		```java
		// secret key 값 검증
		FormWebAuthenticationDetails formWebAuthenticationDetails (FormWebAuthenticationDetails)authentication.getDetails();
		
		String secretKey = formWebAuthenticationDetails.getSecretKey();
		if(secretKey == null || !"secret".equals(secretKey)){
		    throw new InsufficientAuthenticationException("InSufficientAuthenticationException");
		}
		```

	5. `SecuriyConfig`

		```java
		// 생략
		@Override
		    protected void configure(HttpSecurity http) throws Exception {
		        http
		                .authorizeRequests()
		                .antMatchers("/", "/users").permitAll()
		                .antMatchers("/mypage").hasRole("USER")
		                .antMatchers("/messages").hasRole("MANAGER")
		                .antMatchers("/config").hasRole("ADMIN")
		                .anyRequest()
		                .authenticated() // 시스템 접속시 인증 필요
		        .and()
		                .formLogin()
		                .loginPage("/login")
		                .loginProcessingUrl("/login_proc")
		                .authenticationDetailsSource(authenticationDetailsSource) // 추가
		                .defaultSuccessUrl("/")
		                .permitAll()
		        ;
		    }
		```


---

출처 : [스프링 시큐리티 - Spring Boot 기반으로 개발하는 Spring Security](https://www.inflearn.com/course/코어-스프링-시큐리티)