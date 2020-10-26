### **스프링 시큐리티 기본 API 및  Filter를 이해한다**

---

* **Intellij + Spring Boot + Spring Security를 활용하여 프로젝트를 구성한다.**
* **스프링 시큐리티 기본 API 및  Filter를 이해한다**

	1. [예외 처리 및 요청 캐시 필터](#예외-처리-및-요청-캐시-필터)
	2. [CSRF, CsrfFilter](#CSRF,-CsrfFilter)

---

1. ### 예외 처리 및 요청 캐시 필터

  1. **ExceptionTranslationFilter**

    1. `AuthenticationException`

    * **인증 예외 처리**

    	1. `AuthenticationEntryPoint` 호출
    		* 로그인 페이지 이동, 401 오류 코드 전달 등
    	2. **인증 예외가 발생하기 전의 요청 정보를 저장**
    		1. `RequestCache` : 사용자의 이전 요청 정보를 세션에 저장하고 이를 꺼내오는 캐시 메카니즘
    		2. `SavedRequest` : 사용자가 요청했던 request 파라미터 값들, 그 당시의 헤더값들 등이 저장

    2. **AccessDeniedException**

    	* **인가 예외 처리**
    	* `AccessDeniedHandler` 에서 예외 처리하도록 제공
    	
    3. **필터 흐름**

    	![image](https://user-images.githubusercontent.com/52272332/97145643-e2fbd980-17a9-11eb-9e49-2e30e0971152.png)

    	* **사용자가 요청을 보내고 나서 필터링 작업이 시작된다**
    	* **인증없이 접근하면 인증 예외가 아닌 인가 예외로 처리된다. -> 익명 사용자의 접근으로 취급된다.**
    	* **익명사용자 이거나 Remember Me의 경우는 `AccessDeniedHanlder`가 아닌 `AuthenticationException`으로 보낸다.**

    	1. **인가 예외**
    		* 핸들러 호출로 후속 작업을 처리
    	2. **인증 예외**
    		* 익명 사용자의 경우 로그인 페이지로 넘겨줘야 한다.
    		* `ServletContext`를 비우는 작업도 같이 실행 후 로그인 페이지를 준다?

    4. **코드**

    	```java
    	@Override
    	    protected void configure(HttpSecurity http) throws Exception {
    	http
    	    .exceptionHandling()
    	    .authenticationEntryPoint(AuthenticationEntryPoint()) // 인증 예외
    	    .accessDeniedHandler(AccessDeniedHandler()); // 인가 예외 
    	    }
    	```

    	* **전체코드**

    	```java
    	@Configuration
    	@EnableWebSecurity
    	public class SecurityConfig extends WebSecurityConfigurerAdapter {
    	
    	    @Autowired
    	    UserDetailsService userDetailsService;
    	
    	    @Override
    	    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    	
    	        auth.inMemoryAuthentication().withUser("user").password("{noop}1111").roles("USER");
    	        auth.inMemoryAuthentication().withUser("sys").password("{noop}1111").roles("SYS");
    	        auth.inMemoryAuthentication().withUser("admin").password("{noop}1111").roles("ADMIN", "SYS", "USER");
    	        // 특성한 password 유형에 무엇을 사용했는지 명시해줘야한다. 검사할때 이를 보고 해석한다. {noop} 평문
    	    }
    	
    	    @Override
    	    protected void configure(HttpSecurity http) throws Exception {
    	
    	        http
    	                .authorizeRequests()
    	                .antMatchers("/login").permitAll()
    	                .antMatchers("/user").hasRole("USER")
    	                .antMatchers("/sys").hasRole("SYS")
    	                .antMatchers("/admin/pay").hasRole("ADMIN")
    	                .antMatchers("/admin/**").access("hasRole('ADMIN')or hasRole('SYS')")
    	                .anyRequest().authenticated();
    	        http
    	                .formLogin()
    	                .successHandler(new AuthenticationSuccessHandler() {
    	                    @Override
    	                    public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication authentication) throws IOException, ServletException {
    	                        RequestCache requestCache = new HttpSessionRequestCache(); // 인증 성공 후 그 정보를 꺼내는 처리
    	                        SavedRequest savedRequest = requestCache.getRequest(request,response); // 세션에서 정보를 꺼내고
    	                        String redirectUrl = savedRequest.getRedirectUrl();
    	                        response.sendRedirect(redirectUrl); //꺼낸 정보로 이동
    	                    }
    	                })
    	        ;
    	
    	        http
    	                .exceptionHandling()
    	                .authenticationEntryPoint(new AuthenticationEntryPoint() {
    	                    @Override
    	                    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException e) throws IOException, ServletException {
    	                        response.sendRedirect("/login");
    	                    } // 인증해야되는 페이지로 이동시켜줌
    	                })
    	                .accessDeniedHandler(new AccessDeniedHandler() {
    	                    @Override
    	                    public void handle(HttpServletRequest request, HttpServletResponse response, AccessDeniedException e) throws IOException, ServletException {
    	                        response.sendRedirect("/denied");
    	                    } // 인가 실패시 이동되는 페이지를 정해둔다.
    	                });
    	
    	    }
    	}
    	
    	```

    5. **결과**

    	![image](https://user-images.githubusercontent.com/52272332/97146884-f5771280-17ab-11eb-9828-75637566fa0a.png)

    	* **어느 url을 접속하든 `/login`으로 연결된다.**
    	* 해당 부분을 주석 처리 `USER`로 로그인하여 `/admin`으로 접속을 시도해본다.

    	```java
    	//                .authenticationEntryPoint(new AuthenticationEntryPoint() {
    	//                    @Override
    	//                    public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException e) throws IOException, ServletException {
    	//                        response.sendRedirect("/login");
    	//                    } // 인증해야되는 페이지로 이동시켜줌
    	//                })
    	```

    	![image](https://user-images.githubusercontent.com/52272332/97147082-4ab32400-17ac-11eb-8539-7d1b2cb08a94.png)

    	* 유저에게 접근권한이 없으므로 `/denied` 페이지로 이동된다.

  2. **RequestCacheAwareFilter**

  	* **로그인 성공 후, 이전 요청 정보를 재구성하기 위해 사용한다.**
  	* 현재 요청과 관련 있는 캐시된 요청이 있는지 찾아서 적용하는 필터
  		1. 캐시된 요청이 없다면, 현재 요청 처리
  		2. 캐시된 요청이 있다면, 캐시된 요청 처리

2. ### CSRF, CsrfFilter

	1. **CSRF**

		* **Cross site request foregery** : 사이트 간 요청 위조

		* 사용자가 자신의 의지와는 무관하게 공격자의 의도한 행위를 웹 사이트에 요청하게 하는 공격을 말한다.

		* **공격 과정**

			1. 사용자가 웹 사이트에 로그인하여 정상적으로 쿠키를 받는다.

			2. 공격자가 링크나 이메일을 통해 이용자에게 URL을 전달한다.

			3. **공격용 HTML에는 다음과 같은 태그를 가진다.**

				```html
				<img src= "https://travel.service.com/travel_update?.src=Korea&.dst=Hell">
				```

				* 출발지나 도착지를 변조한다.

			4. 이용자가 공격용 URL을 연다.

			5. 사용자가 승인이나 인지 없이 출발지 혹은 도착지가 변경,등록 됨으로써 공격이 완료된다.

			![image](https://user-images.githubusercontent.com/52272332/97154085-d5008580-17b6-11eb-8400-3d4b30beb094.png)

	2. **CsrfFilter**

		* **모든 요청에 랜덤하게 생성된 토큰을 HTTP 파라미터로 요구한다.**

		* 요청 시 전달되는 토큰 값과 서버에 저장된 실제 값과 비교 후 일치하지 않으면 실패

		* **Client** : `<input type="hidden" name="${_csrf.paramterName}" value="${_csrf.toekn}"/>`

		* HTTP : PATCH, POST, PUT, DELETE

		* **Spring Security**

			```java
			http.csrf(); // 기본 활성화
			http.csrf().disabled(); // 비활성화
			```

		* **기본적으로 시큐리티에 내장되어 있기 때문에 사용시 자동으로 된다.**

		* **타임리프의 경우도 마찬가지로 헤더에 토큰을 자동으로 설정해준다. 이를 통해 그냥 사용하면 된다.**