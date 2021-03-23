### 실전프로젝트 -인증 프로세스 Form 인증 구현

---

1. [DB 연동 인증 처리 (1) : CustomUserDetailsService](#DB-연동-인증-처리-(1)-:-CustomUserDetailsService)
2. [DB 연동 인증 처리 (2) : CustomAuthenticationProvider](#DB-연동-인증-처리-(2)-:-CustomAuthenticationProvider)
3. [UserDetailsService vs AuthenticationProvider](#UserDetailsService-vs-AuthenticationProvider)

---

1. ### DB 연동 인증 처리 (1) : CustomUserDetailsService

	* **회원가입 정보를 DB에 저장**
	* **`UserDetailsService` 인터페이스를 통해 DB에서 유저 정보를 가져온다**
	* **저장된 정보를 토대로 유저에게 권한 부여**

	1. **구조**

		![image](https://user-images.githubusercontent.com/52272332/111435038-a1ad9b80-8743-11eb-9744-3e9f0aed5d42.png)

	2. **코드**

		* `AccountContext`

			```java
			import org.springframework.security.core.userdetails.User;
			
			public class AccountContext extends User {
			
			    private final Account account;
			
			    public AccountContext(Account account, Collection<? extends GrantedAuthority> authorities) {
			        super(account.getUsername(), account.getPassword(), authorities);
			        this.account=account;
			    }
			
			    public Account getAccount(){
			        return account;
			    }
			}
			```

			* `User` class를 상속받고 `Account` 객체를 사용해서 전달받는다.

		* `CustomUserDetailsService`

			```java
			@Service("userDetailsService")
			public class CustomUserDetailsService implements UserDetailsService {
			
			    @Autowired
			    private UserRepository userRepository;
			
			    @Override
			    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
			
			        Account account = userRepository.findByUsername(username);
			
			        if(account == null){
			            throw new UsernameNotFoundException("UsernameNotFoundExpection");
			        }
			        List<GrantedAuthority> roles = new ArrayList<>();
			        roles.add(new SimpleGrantedAuthority(account.getRole()));
			
			        AccountContext accountContext = new AccountContext(account,roles);
			        // 기본 권한
			
			        return accountContext;
			    }
			}
			```

			* `UserDetails loadUserByUsername(String name)`을 `@Override` 
			* `userRepository.findByUsername(username);`를 통해 `Account`를 `DB`에서 조회 후 찾아온다.
			* `null`인 경우 `UsernameNotFoundException`
			* 아닐 경우 `List<GrantedAuthority> roles = new ArrayList<>();`를 생성하고
			* `        roles.add(new SimpleGrantedAuthority(account.getRole()));`를 통해 `Account`의 `role`를 가져와 추가하고 `accountContext`를 `return`한다.
			* **일치하는 아이디 정보를 DB에서 꺼낸 뒤 아이디와 권한을 가진 `AccountContext`를 `return`하는 구조**

		* `SecurityConfig`

			```java
			@Configuration
			@EnableWebSecurity
			public class SecurityConfig extends WebSecurityConfigurerAdapter {
			
			    @Autowired
			    private UserDetailsService userDetailsService;
			
			    @Override
			    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
			        // CustomUserDetailsService를 통해 검증하도록 설정한다. 
			        auth.userDetailsService(userDetailsService);
			    }
			    // 생략....
			}
			
			```

	3. **실행화면**

		* 회원가입

			![image](https://user-images.githubusercontent.com/52272332/111436347-382e8c80-8745-11eb-82c3-80cb723428a9.png)

		* **DB저장 확인**

			![image](https://user-images.githubusercontent.com/52272332/111436541-7166fc80-8745-11eb-8009-89fce4f04302.png)

		* **`MANAGER` 권한으로 접근 가능한 `/messages`접근**

			![image](https://user-images.githubusercontent.com/52272332/111436347-382e8c80-8745-11eb-82c3-80cb723428a9.png)

2. ### DB 연동 인증 처리 (2) : CustomAuthenticationProvider

	* **userDetailsSerivce가 DB에 저장된 정보로 `AccountContext`를 넘기면**
	* **인자로 넘겨받은 비밀번호와 DB에 저장된 `Encode`된 비밀번호의 일치 여부를 확인 후 `Token`을 `return` 한다.**

	1. **구조**

		* `package io.security.coresecurity.lecture.security.provider;` 위치에 `CustomAuthenticationProvider` 추가

		```java
		package io.security.coresecurity.lecture.security.provider;
		
		public class CustomAuthenticationProvider implements AuthenticationProvider {
		
		    @Autowired
		    private UserDetailsService userDetailsService;
		
		    @Autowired
		    private PasswordEncoder passwordEncoder;
		
		    @Override
		    public Authentication authenticate(Authentication authentication) throws AuthenticationException {
		        // 검증 구현
		        String username = authentication.getName();
		        String password = (String)authentication.getCredentials();
		
		        
		        AccountContext accountContext = (AccountContext) userDetailsService.loadUserByUsername(username);
		        
		        // 패스워드 일치 여부 검증 
		        if(!passwordEncoder.matches(password,accountContext.getAccount().getPassword())){
		            throw new BadCredentialsException("BadCrednetialsException");
		        }
		
		        UsernamePasswordAuthenticationToken authenticationToken = new UsernamePasswordAuthenticationToken(accountContext.getAccount(), null, accountContext.getAuthorities());
		
		        return authenticationToken;
		    }
		
		    @Override
		    public boolean supports(Class<?> authentication) {
		        // 현재 전달된 class의 타입과 class가 사용하고자 하는 token과 일치하면 인증처리
		
		        return UsernamePasswordAuthenticationToken.class.isAssignableFrom(authentication);
		    }
		}
		```