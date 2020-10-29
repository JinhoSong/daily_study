### **스프링 시큐리티 기본 API 및  Filter를 이해한다**

---

* **Intellij + Spring Boot + Spring Security를 활용하여 프로젝트를 구성한다.**
* **스프링 시큐리티 기본 API 및  Filter를 이해한다**

	1. [동시 세션 제어, 세션 고정 보호, 세션 정책](#동시-세션-제어,-세션-고정-보호,-세션-정책)
	2. [세션 제어 필터](#세션-제어-필터)
	3. [권한 설정 및 표현식](#권한-설정-및-표현식)

---

1. ### 동시 세션 제어, 세션 고정 보호, 세션 정책
    1. **동시 세션 제어**

        ![image](https://user-images.githubusercontent.com/52272332/96845941-3b6e6680-148c-11eb-9894-b65d20b7fb59.png)

        * **동시 세션이 들어오는 경우 처리 방법은 2가지 나뉜다.**

            1. **이전 사용자의 세션을 만료시키는 경우**
                * 같은 user가 로그인을 시도하는 경우 서버는 해당 user의 이름으로 이미 세션이 있다면
                * 신규 요청이 아닌 마지막으로 들어왔던 사용자의 세션을 만료시키고
                * 새로운 세션을 신규 요청자에게 발급한다.
                * **이전 사용자가 인증이 필요한 곳에 다시 접속하는 경우 세션만료 처리 알림이 가도록한다**
            2. **현재 사용자의 인증을 실패시키는 경우**
                * 이미 같은 user에게 발급한 세션이 존재한다면
                * 새로 로그인 하는 user에게는 **인증 예외를 보낸다**
                * 이로써 세션은 서버에서 지정한 숫자만큼만 유지된다.

        * `SecurityConfig.java`

            ```java
            @Configuration
            @EnableWebSecurity
            public class SecurityConfig extends WebSecurityConfigurerAdapter {
                http
                    .sessionManagement()
                    .maximumSessions(1) // 촤대 허용 동시 세션 수 (-1)은 무제한
                    .maxSessionsPreventsLogin(false)// false = 동시로그인 차단, true= 기존 세션 만료 
                    .invalidSessiongUrl("/invalid") // 세션이 유효하지 않을때 이동 페이지
                    .expiredUrl("/expired") //세션이 만료된 경우 이동 페이지 
                    ;
                }
            }
            
            ```

        * **실행**

            1. `.maxSessionsPreventsLogin(false)`

                * **이전 세션을 만료 시키는 경우**
                * 우선 크롬창으로 로그인 한 뒤 동일한 user로 IE에서 로그인한다.
                * 그 후 크롬창을 F5해본다.

                ![image](https://user-images.githubusercontent.com/52272332/96847227-e3386400-148d-11eb-929a-76db2c40bc16.png)

                * **이전 세션이였던 크롬창의 세션이 만료되어 접속이 불가능하다는 메시지가 나온다**

            2. `.maxSessionsPreventsLogin(true)`

                * **신규 로그인을 막는 경우**
                * **위 테스트와 동일하게 크롬창으로 우선 로그인 후 IE로 로그인을 시도한다.**

                ![image](https://user-images.githubusercontent.com/52272332/96847711-7a052080-148e-11eb-89a9-55332d22011e.png)

                * **IE에서 세션 갯수 초과로 인해 로그인이 불가한다는 메시지가 뜬다**
        
    2. **세션 고정 보호**

        * **세션 고정 보호란 인증에 성공할때 마다 세션번호를 새로 발급해주는걸 의미한다.**

        ![image](https://user-images.githubusercontent.com/52272332/96848617-99507d80-148f-11eb-952d-ae4fb8a60c7d.png)

        * **공격 시나리오**

        	1. 공격자가 webApp에 접속하여 **JESSIONID**을 발급받는다.
        	2. 공격자가 몰래 사용자에게 자신의 **JESSIONID**쿠키를 사용하도록 설정한다.
        	3. **사용자가 로그인에 성공하였을 때, 공격자는 로그인 절차 없이 WebApp에 자유롭게 접근이 가능해진다.**

        * `SecurityConfig`

        	```java
        	http.
        	    sessionManagemnt()
        	    .sessiongFixation().changeSessionId() // 기본값
        	    // none, migrateSession, newSession이 존재한다.
        	    ;
        	```

        * **세션이 들어오는 경우 새롭게 발급하여 번호를 제공하므로 이전 세션번호와는 연관이 없어지므로 공격자가 접근할 수 없다.**

    3. **세션 정책**

        * `Security.Config`

        	```java
        	http
        	    .sessionManagement()
        	    .sessiongCreateionPolicy(SessionCreationPolicy.If_Requried)
        	    ;
        	/* 
        		SessiongCreateionPolicy.
        			Always : 항상 세션 생성
        			If_Required : 필요 시 생성 ( 기본값 )
        			Never : 생성하지 않지만 사용은 함
        			Stateless : 존재해도 사용하지 않음 -> JWT 이용시 적용 
        			어떤걸 사용할지는 알아서 정해야 한다.
        	*/
        	```

2. ### 세션 제어 필터

    1. `SessionManagementFilter`

    	* **세션에 관련된 각종 기능을 제공한다**

    	1. **세션 관리**
    		* 인증 시 사용자의 세션정보를 등록, 조회, 삭제 등의 세션 이력을 관리
    	2. **동시적 세션 제어**
    		* 동일 계정으로 접속하는 최대 세션수를 제한
    	3. **세션 고정 보호**
    		* 인증 할 때마다 세션쿠키를 새로 발급하여 공격자의 쿠키 조작 공격을 방지한다.
    	4. **세션 생성 정책**
    		* 세션 생성할지 말지, 사용할지 말지 정한다.

    2. `ConcurrentSessiongFiliter`

    	* **매 요청시 마다 사용자의 세션 만료 여부를 체크한다**
    	* 세션이 만료되었을 경우 즉시 처리한다.

    	* `session.isExpried==true`
    		1. 로그아웃 처리
    		2. 즉시 오류 페이지 응답 -> **"This session has been expired"**
    		3. **해당 값이 여러 필터링의 `if`문에 사용된다.**

    3. **필터링 과정**

    	![image](https://user-images.githubusercontent.com/52272332/96862709-cb1e1000-14a0-11eb-8345-d8e8e21d9f92.png)

    	1. 새로운 사용자가 동일한 userId로 로그인 성공했다면
    	2. `SessionMananagementFiliter`가 이전 사용자의 세션을 만료시킨다.
    	3. 이전 사용자가 서버에 어떠한 요청을 보내고 그 요청이 인증을 요구할때
    	4. `ConcurrentSessionFiliter`가 해당세션을 검사하고 만료되었다면 로그아웃, 오류 페이지 응답을 수행한다.

    4. **필터링 과정 심화**

    	![image-20201022200636690](C:\Users\chosu\AppData\Roaming\Typora\typora-user-images\image-20201022200636690.png)
    
3. ### 권한 설정 및 표현식

    * **권한 설정 방식**

    	1. **선언적 방식**

    		* URL : __http.antMatchers("/user/**").hasRole("USER")__

    		* **Method**

    			`@PreAuthorize("hasRole('USER')")`

    			`public void user(){ System.out.println("user") }`

    	2. **동적 방식 - DB 연동**

    		* URL
    		* Method

    * **인가 설정 기본 코드**

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
    	                .antMatchers("/user").hasRole("USER")
    	                .antMatchers("/sys").hasRole("SYS")
    	                .antMatchers("/admin").access("hasRole('ADMIN')or hasRole('SYS')")
    	     
    	    }
    	}
    	
    	```

    	* **구체적인 경로가 먼저오고 큰 범위는 나중에 오도록 한다 -> 뒤에 설명**

    * **표현식**

    	|        메소드         |                         동작                          |
    	| :-------------------: | :---------------------------------------------------: |
    	|    authenticated()    |           인증된 사용자의 접근을 허용한다.            |
    	| fullyAuthenticated()  |   인증된 사용자의 접근을 허용, rememberMe 인증 제외   |
    	|      permitAll()      |                  무조건 접근을 허용                   |
    	|      anonymous()      |               익명사용자의 접근을 허용                |
    	|    access(String)     | **주어진 SpEL 표현식의 평가 결과가 true면 접근 허용** |
    	|    hasRole(String)    |           **사용자가 주어진 권한이 있다면**           |
    	| hasAnyRole(String...) |       사용자가 주어진 권한이 있다면 접근을 허용       |

    * **실습**

    	```java
    	 @Override
    	    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    	
    	        auth.inMemoryAuthentication().withUser("user").password("{noop}1111").roles("USER");
    	        auth.inMemoryAuthentication().withUser("sys").password("{noop}1111").roles("SYS");
    	        auth.inMemoryAuthentication().withUser("admin").password("{noop}1111").roles("ADMIN");
    	        // 특성한 password 유형에 무엇을 사용했는지 명시해줘야한다. 검사할때 이를 보고 해석한다. {noop} 평문
    	    }
    	```

    	* 먼저 `id,passoword,권한`을 가지고 있는 사용자들을 설정한다.

    	```java
    	@Override
    	    protected void configure(HttpSecurity http) throws Exception {
    	
    	        http
    	                .authorizeRequests()
    	                .antMatchers("/user").hasRole("USER")
    	                .antMatchers("/sys").hasRole("SYS")
    	                .antMatchers("/admin/pay").hasRole("ADMIN")
    	                .antMatchers("/admin/**").access("hasRole('ADMIN')or hasRole('SYS')")
    	                .anyRequest().authenticated();
    	    }
    	}
    	```

    	1. **인가정책 규칙을 설정한다.**
    	2. **정책은 프로그램이 위에서부터 아래로 읽으면서 수행한다.**
    	3. **즉, 위에서 선언한 권한과 매핑이 성공할 경우 아래는 수행하지 않는다.**
    	4. 따라서 큰 범위의 `admin/**`가 아래에 위치하고 작은 범위인 `admin/pay`가 위에 배치해야한다!!!!

    * **실행결과**

    	1. **USER권한 로그인**

    		![image](https://user-images.githubusercontent.com/52272332/97138374-695cef00-179b-11eb-9435-85057f440bbd.png)

    		* **USER**권한의 url에는 접근이 가능하지만

    		![image](https://user-images.githubusercontent.com/52272332/97138413-7da0ec00-179b-11eb-982a-449a4023c6e8.png)

    		* **ADMIN** 권한의 URL에는 접근이 불가능하다.

    	2. **ADMIN권한 로그인**

    		![image](https://user-images.githubusercontent.com/52272332/97138450-96a99d00-179b-11eb-8512-16b59855309e.png)

    		* **ADMIN** 권한의 로그인이 가능하지만 반대로

    		![image](https://user-images.githubusercontent.com/52272332/97138518-bc36a680-179b-11eb-9838-8a0cce755814.png)

    		* **USER** 권한에 접근하지 못하게 된다.

    	3. **여러 권한 부여하기**

    		```java
    		auth.inMemoryAuthentication().withUser("admin").password("{noop}1111").roles("ADMIN","SYS","USER");
    		
    		```

    		* **여러 권한을 부여하기 위해 roles()로 처리하였다.**
    		* 하드코딩 형태가 아닌 방법은 추후에 더 학습할 예정
    		* `{noop}`은 비밀번호가 평문으로 들어갔다는 정보를 알려주는 것이다. **시큐리티 5버전 이상부터는 사용해야한다**

