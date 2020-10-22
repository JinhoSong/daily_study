### **스프링 시큐리티 기본 API 및  Filter를 이해한다**

---

* **Intellij + Spring Boot + Spring Security를 활용하여 프로젝트를 구성한다.**
* **스프링 시큐리티 기본 API 및  Filter를 이해한다**

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

