### **스프링 시큐리티 기본 API 및  Filter를 이해한다**

---

* **Intellij + Spring Boot + Spring Security를 활용하여 프로젝트를 구성한다.**
* **스프링 시큐리티 기본 API 및  Filter를 이해한다**

	1. [Remember Me 인증](#Remember-Me-인증)
	2. [RememberMeAuthenticationFilter](#RememberMeAuthenticationFilter)
	3. [AnonymousAuthenticationFilter](#AnonymousAuthenticationFilter)

---

1. ### Remember Me 인증

	* **세션이 만료되고 웹 브라우저가 종료된 후에도 어플리케이션 사용자를 기억하는 기능**

	* Remember-Me 쿠기에 대한 Http 요청을 확인한 후 **토큰 기반 인증을 사용해 유효성을 검사하고 토큰이 검증되면 사용자는 로그인 된다.**

	* **사용자 라이프 사이클**

		1. 인증 성공 ( Remember Me 쿠기 설정)
		2. 인증 실패 ( 쿠기가 존재하면 쿠키 무효화)
		3. 로그아웃 ( 쿠키가 존재하면 쿠키 무효화)

		![image](https://user-images.githubusercontent.com/52272332/96827248-1c61db80-1470-11eb-9658-159ff815a0b2.png)

	* **체크박스가 이를 뜻한다**

	* `http.remememberMe()` 

		* **기능이 작동하도록 한다.**

		```java
		protected void configure(HttpSecurity http) throws Exception{
		    @Autowired
		    UserDetailsService userDetailsService;
		http.rememberMe()
		    .rememberMeParameter("remeber") // 기본 파라티터명은 Remember-me
		    .tokenValiditySeconds(3600) // Default는 14일이고 초 단위로 계산
		    .alwaysRemember(true) // 항상 실행
		    .userDetailsService(userDetailsService); // 유저 검사용 
		}
		```

	* **적용하기**

		`SecurityConfig.java`

		```java
		@Configuration
		@EnableWebSecurity
		public class SecurityConfig extends WebSecurityConfigurerAdapter {
		
		    @Autowired
		    UserDetailsService userDetailsService;
		
		    @Override
		    protected void configure(HttpSecurity http) throws Exception {
		        http
		                .authorizeRequests()
		                .anyRequest().authenticated();
		        http
		                .formLogin();
		
		        http
		                .logout()
		                .logoutUrl("/logout")
		                .logoutSuccessUrl("/login") // url만 보냄
		                .addLogoutHandler(new LogoutHandler() {
		                    @Override
		                    public void logout(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Authentication authentication) {
		                        HttpSession session = httpServletRequest.getSession(); // 세션을 받고
		                        session.invalidate(); // 세션 초기화
		                    }
		                })
		                .logoutSuccessHandler(new LogoutSuccessHandler() {
		                    @Override
		                    public void onLogoutSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
		                        response.sendRedirect("/login");
		                    }
		                })
		            .and() // 추가된 rememberMe 코드
		                .rememberMe()
		                .rememberMeParameter("remeber")
		                .tokenValiditySeconds(3600)
		                .userDetailsService(userDetailsService);
		    }
		}
		
		```

	* **적용 확인**

		1. 프로젝트 활성화 후 기존 방식으로 로그인

		2. **크롬의  EditThisCookie 확장프로그램을 추가 후 로그인 후 저장된 세션ID을 확인한다**

			![image](https://user-images.githubusercontent.com/52272332/96827764-3fd95600-1471-11eb-8d22-07b080368aef.png)

		3. **확인된 아디를 지운 후 F5를 눌러 다시 접근해본다**

			![image](https://user-images.githubusercontent.com/52272332/96827980-b413f980-1471-11eb-9d94-5d38e7a114ba.png)

			* **세션 ID를 잃었기 때문에 스프링 시큐리티가 인증된 사용자라고 인식하지 않고 다시 로그인을 요구한다.**

		4. **이번엔 Remember me에 체크 표시 후 로그인한다.**

			![image](https://user-images.githubusercontent.com/52272332/96828094-ede50000-1471-11eb-9276-ff32a38c3ae6.png)

			* **새로운 쿠키인 remember-me가 생성된다.**

		5. **동일한 방법으로 JSESSIONID쿠키를 지운 뒤 F5를 누른다.**

			![image](https://user-images.githubusercontent.com/52272332/96828190-22f15280-1472-11eb-96f1-919810425b3b.png)

			* 새로운 JESSIONID와 함께 인증을 다시 요구하지 않는것을 확인할 수 있다.

2. ### RememberMeAuthenticationFilter

    ![image](https://user-images.githubusercontent.com/52272332/96832929-897a6e80-147a-11eb-946c-e93f7073e3ca.png)
    
       * **필터가 사용자 요청을 처리하는 조건이 있다!!**

       1. `RememberMeAuthenicationFilter`

           1. **Authenication 객체가 null인경우**
           * **인증 객체는 SecurityContext에 저장되어있다.**
             
           * 인증을 받은 사용자는 늘 해당 객체에 존재한다.
             
           * 하지만 null인 경우는 
             
               1. **세션이 만료되었거나**
                   2. 세션이 존재하지 않거나
                   3. 브라우저가 종료된 경우
                   4. SC에서 찾지 못하는 경우
           2. **remember me 쿠키값을 가지고 접속한 경우**
               * 해당 쿠키로 인증한다.
           
       2. `RememberMeServices`

           * 두개의 구현체가 있다

           1. `TokenBasedRememberMeService`
           	* 메모리에서 실제로 저장한 토큰과 사용자가 요청한 토큰과 비교해서 인증처리
           	* **만료기간이 있다**
           2. `PersistentToKenBasedRememberMeService`
           	* DB에 세션을 저장하고
           	* **영구적이다**

       3. `Decode Token`

           * 토큰에 저장된 User 정보와 DB의 User정보와 비교해서 존재하면 새로운 세션을 준다.
    
3. ### AnonymousAuthenticationFilter

    ![image](https://user-images.githubusercontent.com/52272332/96837963-2987c600-1482-11eb-8ea3-9f88873f4d5e.png)

    * **인증 받지 않은 사용자를 null로 처리하지만 `AnonymousAuthenticationFilter`는 별도로 처리한다.**
    * `AnonymousAuthenticationFilter`
    	1. **익명사용자 인증 처리 필터**
    	2. 익명 사용자와 인증 사용자를 구분해서 처리하기 위한 용도
    	3. 화면에서 인증 여부를 구현할때 `isAnonymous()` 와 `isAuthenicated()`로 구분해서 사용
    	4. 인증객체를 세션에 저장하지 않는다.
    	5. **익명 사용자용 객체를 사용한다.**