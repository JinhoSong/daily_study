**Servlet Container**

---

1. **Sevlet**

  * **Servlet 이란**
  	* **웹 기반의 요청에 대한 동적인 처리가 가능한 하나의 클래스이다.**
  	* **개발자가 직접 작성해 비즈니스 로직이 처리되는 곳이다.**
  	* 요청 단위당 Thread로 동작한다.
  	* `HttpServlet`이 핵심 클래스이다.
  	* **장점**

        1. 빠르다
        2. 플랫폼이 독립적이다
        3. 보안, 이식성, 유지보수, 기능 확장이 용이하다.
  	* **단점**
        1. 화면에 표현할 HTML을 작성해야한다.

        2. **반드시 컴파일 된 후에 작동한다**

2. **Static Pages vs Dynamic Pages**

  1. **정적 페이지**

       * 서블릿을 사용하는 이유는 **동적은 페이지 생성을 위해서이다.**
       * 기존 정적의 페이지는 아래와 같은 구조를 가진다.

       <img src="https://user-images.githubusercontent.com/52272332/97532263-4af42f00-19f9-11eb-89e2-f770d7b9e988.png" alt="image" style="zoom:50%;" />

       * **동작 순서**
           1. 사용자가 request를 보낸다.
           2. web Server가 해당 요청에 맞는 Date를 DB에서 찾는다.
           3. 해당 데이터를 `<file contents>`의 형태로 web Server에게 전달한다.
           4. web Server가 사용자에게 해당 값을 넘겨주거나 보여준다.
       * **문제점**
           1. 다른 사용자에게 다른 정보를 보여주고 싶은 경우 처리가 불가능하다.
           2. 모두가 같은 화면만 보도록 설계된 서버이다.
       
  2. **동적 페이지**

       * **정보마다 페이지가 다르게 나오도록 해준다.**

       <img src="https://user-images.githubusercontent.com/52272332/97532694-395f5700-19fa-11eb-9f73-656416a862b8.png" alt="image" style="zoom:50%;" />

       * **`doGet, doPost` 같은 메서드가 이미 정의되어 있다.**
       * 해당 메서드를 재정의 하는것이 개발자가 할 일이다.
       * **동작 순서**
       	1. 사용자가 request를 날린다.
       	2. 해당 request를 JVM 안의 Servlet에게 전달된다.
       	3. Servlet이 DB에 데이터를 조회하고
       	4. 해당 데이터에 맞는 뷰를 생성 후
       	5. 사용자에게 response한다.
       	6. 사용자가 요구한 데이터마다 다른 화면이 출력된다.

3. **Servlet Program**

	![image](https://user-images.githubusercontent.com/52272332/97536258-323b4780-1a00-11eb-98ef-9e7fff391f3f.png)

	* **실행순서**
		1. Client가 웹 서버에 request를 보낸다.
		2. 웹 서버는 해당 url을 **Sevlet Container**에 전달한다.
		3. **Sevlet Container** 는 해당 url에 맞는 Sevlet을 찾아서 실행한다.
		4. 해당 Sevlet을 실행할때는 Thread 단위로 실행한다.
			* **`init()`**
				1. 한 번만 수행한다.
				2. 요청한 Sevlet이 생성되고 해당 Sevlet이 메모리에 적재될 때 호출된다.
			* **`Sevice()`**
				1. request에 대한 응답을 구현하는 메소드이다.
				2. `HttpSevlet`을 상속받은 Servlet 클래스에서 `Service()`를 오버라이딩해서 실행하고 없다면 부모의 메서드를 호출한다.
				3. **`Service()` 메서드는 템플릿 패턴으로 구현되어있다.**
					* **부모가 정해둔 일을 자식들이 그대로 수행하거나 필요한 부분의 메소드만을 재정의해서 실행하도록 하여 코드의 중복을 최소화한 패턴이다.**
				4. **request 타입에 따라 적절한 메서드를 호출한다**
				5. return시 Thread가 제거 된다.
			* **`destory()`**
				1. 종료시 한번만 수행한다.
				2. **Servlet 객체를 메모리에서 제거한다**

4. **Servlet Container**

	* **서블릿 컨테이너란?**

		1. 서블릿을 동작시키기 위한 실행 환경
		2. 세션관리, 네트워크 관리, 서블릿 라이프사이클 관리 등을 한다.
		3. 대표적으로 톰캣, 제티 등이 있다.

	* **병행성 제어**

		![image](https://user-images.githubusercontent.com/52272332/97537346-05882f80-1a02-11eb-8239-49f74322c18d.png)

		* **서블릿 컨테이너 / 웹 서버는 모두 멀티스레드 환경이다.**
		* 즉, 한번의 메모리 적재 이후 해당 메모리르 멀티 쓰레드 환경으로 이용하기 때문에 트랜**병행성 제어**가 필요하다.
		* **변수의 종류**
			1. **Static, member**
				* 공유하는 자원으로 반드시 병행성 제어가 필요하다.
			2. **Local**
				* Thread마다 독립적으로 생성

5. **Servlet Annotations**

	* 서블릿을 표현하는데는 여러가지 방법이 존재한다.

	1. **XML**

	![image](https://user-images.githubusercontent.com/52272332/97538309-83990600-1a03-11eb-9cc1-80efabbd94b9.png)

	2. **Annotations**

		![image](https://user-images.githubusercontent.com/52272332/97538406-ab886980-1a03-11eb-8fda-e9ef8b2fbd16.png)

8. **References**

  * Java Servlets Tutorial

  	http://www.tutorialspoint.com/servlets/servlets_tutorial.pdf, 2015
