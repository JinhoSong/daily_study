### **스프링 시큐리티 기본 API 및  Filter를 이해한다**

---

* **Intellij + Spring Boot + Spring Security를 활용하여 프로젝트를 구성한다.**
* **스프링 시큐리티 기본 API 및  Filter를 이해한다**
	1. [Form Login 인증 필터 : UsernamePasswordAuthenicationFiliter](#Form-Login-인증-필터-:-UsernamePasswordAuthenicationFiliter)
	2. [Logout 처리 LogoutFilter](#Logout-처리-LogoutFilter)

---

1. ### Form Login 인증 필터 : UsernamePasswordAuthenicationFiliter

	* **인증 처리에 관한 요청을 받고 처리하는 필터이다**

	* **구조**

		![image](https://user-images.githubusercontent.com/52272332/96558849-388e3d00-12f7-11eb-945c-15453b688f5b.png)

	1. `UsernamePasswordAuthenticationFiliter` 

		* 인증 요청을 보낸다.

	2. `AntPathRequestMatcher("/login")` 

		* 인증 처리가 `/login`으로 넘어 오는지 확인한다. 아니면 다음 필터로 보내고
		* 이는 저번 작업에 수행했던 `.loginProcessingUrl("/여기값")` 으로 변경이 가능하다.

	3. `Authentication`

		* `Authentication` 객체를 만들고 username, password를 저장한다.
		* 후 인증 처리를 맡긴다.

	4. `AuthenticationManager`

		* **이 객체를 기준으로 위는 인증처리 전의 작업들이고 아래는 인증처리 후 작업이다**

		* 인증 객체를 전달받고 처리한다.
		* 내부적으로 `AuthenticationProvider`에게 인증 처리를 위임한다.
		* 해당 클래스로 성공, 실패 여부 리턴값을 받는다.
		* **성공시 받은 최종 객체를 return 한다**

	5. `Authentication`

		* 최종적으로 인증에 성공한 username
		* 해당 유저의 권한 정보들도 저장되어 있다.

	6. `SecurityContext`

		* **인증 객체를 저장하는 저장소**
		* **전역적으로 참조되도록 처리된다.**

2. ### Logout 처리 LogoutFilter

	![image](https://user-images.githubusercontent.com/52272332/96563690-34651e00-12fd-11eb-8321-f3043d6d705a.png)

	* 로그아웃 요청시 

		1. 세션 무효화
		2. 인증 토큰 무효화
		3. 인증 토큰이 저장된 객체 삭제
		4. 쿠키 정보 삭제
		5. 후 로그인 페이지로 다시 이동한다.

	* `http.Logout()`

		* **로그아웃 기능이 작동한다.**

			```java
			protected void configure(HttpSecurity http) throws Exception {
			http.logout()	// 로그아웃 처리
			    .logoutUrl("/logout") // 로그아웃 처리 url
			    .logoutSuccessUrl("/login") // 성공 후 url
			    .deleteCookies("JSESSION", "remember-me")// 로그아웃 후 쿠기 삭제 
			    .addLogoutHandler(LogoutHandler()) // 로그아웃 핸들러 
			    .logoutSuccessHandler(logoutSuccessHandler()); // 로그아웃 성공 후 핸들러
			}
			```

	* `SecurityConfig.java`

		```java
		@Configuration
		@EnableWebSecurity
		public class SecurityConfig extends WebSecurityConfigurerAdapter {
		
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
		                .deleteCookies("remember-me"); // 서버에서 만든 쿠키를 삭제하고 싶으면 해당 이름을 넣으면 된다.
		
		
		    }
		}
		
		```

	* `LogoutForm`

		* [http://localhost:8080/logout](#http://localhost:8080/logout)

		![image](https://user-images.githubusercontent.com/52272332/96566377-75aafd00-1300-11eb-87ab-324472980142.png)

		* **로그아웃은 POST로 전달되어야 한다**
		* 해당 버튼 클릭시 로그아웃이 진행된다.

