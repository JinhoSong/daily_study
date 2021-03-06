### IT 기술면접 공부하기

---

1. ### JAVA

  * **JAVA vs c/c++**

  	* java와 c/c++의 가장 큰 차이점은 **실행 환경이다.**
  	* java에서의 개발 -> 컴파일 or 컴파일 + jar 압축
  	* **자바는 링크 과정이 없어 컴파일러가 바로 바이트 코드를 생성**
  	* c/c++에서의 개발 -> 컴파일 + 링크
  	* **링크** : 여러 분리된 파일들을 컴파일한 각각의 결과물에서 최종적인 실행 파일을 만드는데 필요한 부분들을 찾아 연결하는 작업입니다.
  	* **자바는 jvm이 알아서 동적링크 해준다**

  * **장단점**

    * **장점**
    	* 운영체제에 독립적이다
    		* JVM에서 동작하기 때문에 특정 운영체제에 종속되지 않는다.
    	* 객체지향 언어
    		* 객체지향적 프로그래밍을 위한 여러 언어적 지원을 하고 있다
    			* 추상화 : 객체에서 공통된 속성과 행위를 추출하는 것
    			* 캡슐화 : 프로그램의 세부 구현을 외부로 드러나지 않도록 특정 모듈 내부로 감추는 것
    			* 상속 : 기존의 클래스를 재사용하여 새로운 클래스를 작성하는 것
    			* 다형성 : 다양한 형태로 나타날 수 있는 능력
    				* **오버로딩** : 같은 이름의 메소드를 여러개 정의
    	* **자동으로 메모리 관리를 해준다.**
    		* JVM에서 Grabage Collector라고 불리는 데몬 쓰레드에 의해 GC가 일어난다. GC로 인해 별도의 메모리 관리가 필요 없으며 비지니스로 로직에 집중 가능 
    		* **Garbage Collection 이 일어나면 Unreachable 오브젝트들은 메모리에서 제거된다.** `string`은 불변객체인걸 예시로 들었다 기억하기 
    		* **c/c++** 에서는 OS 메모리에 직접 접근하기 때문에 `free()`메소드를 호출하여 할당받은 메모리를 직접 해제해주어야 한다. 
    		* **JVM은 C로 쓰여졌고 알아서 `free()`를 해준다.** 
    		* 다 맡기니까 검증된걸 써야한다. 
    	* **오픈소스이다.**
    		* OpenJDK가 오픈소스이며, OracleJDK 사용목적에 의해 유료가 될 수 있다.
    		* 생태계 구축이 잘 되어있다.
    	* **멀티스레드를 쉽게 구현 가능**
    		* 자바는 스레드 생성 및 제어와 관련된 라이브러리 API를 제공하고 있기 때문에 실행 되는 운영체제에 상관없이 멀티 스레드를 쉽게 구현할 수 있다.
    	* **동적 로딩 지원**
    		* App 실행시 모든 객체가 생성되는게 아니라 필요한 시점에 동적 로딩으로 생성한다.
    		* 유지보수에 편리하고 빠르다.
    		* 전체 다시 컴파일할 필요가 없다.
    * **단점**
    	* 비교적 속도가 느리다.
    		* 자바는 한 번의 컴파일링으로 실행 가능한 기계어가 만들어지지 않고 JVM에 의해 기계어로 번역되고 실행하는 과정을 거친다.
    		*  C나 C++의 컴파일 단계에서 만들어지는 완전한 기계어보다는 속도가 느리다. 
    		* 하드웨어의 성능 향상과 바이트 코드를 기계어로 변환해주는 JIT 컴파일러 같은 기술 적용으로 JVM의 기능이 향상되어 속도의 격차가 많이 줄어들었다.

  * **Stack memory vs Heap memory**

  	* 내용
  		1. 코드 영역
  			* 메모리의 코드 영역은 실행할 프로그램의 코드가 저장되는 영역으로 텍스트 영역이라고도 부른다.
  			* cpu는 코드 영역에 저장된 명령어를 하나씩 가져와서 처리
  		2. 데이터 영역
  			* 메모리의 데이터 영역은 프로그램의 전역 변수와 정적 변수가 저장되는 영역
  			* 데이터 영역은 프로그램의 시작과 함께 할당되며, 프로그램이 종료 되면 소멸한다.
  		3. **스택 영역**
  			* 함수의 호출과 관계되는 지역 변수와 매개변수가 저장되는 영역
  			* 함수의 호출과 함께 할당되며, 함수의 호출이 완료되면 소멸
  			* 스택 영역은 푸쉬 동작으로 저장 , 팝 동작으로 인출
  			* 가장 늦게 저장된 데이터가 가장 먼저 인출
  			* **메모리의 높은 주소에서 낮은 주소 방향으로 할당**
  		4. **힙 영역**
  			* 사용자가 직접 관리할 수 있는 **그리고 해야 하는 메모리 영역**
  			* 사용자에 의해 동적 할당, 해제 된다.
  			* 메모리 낮은 주소에서 높은 주소
  	* **Stack ( 정적 메모리 할당) 이란?**
  		* JVM에서도 스택 사용
  		* **각각의 스레드는 1개의 스택을 가지고 모든 메소드들은 트랙킹한다**
  		* 새로운 메소드들이 호출 될 때 마다, 새로운 프레임이 스택에 삽입되고, 메소드가 끝날 때 마다 스택에서 제거된다.
  		* **정수, 실수, 논리 변수는 실제값을 저장하고**
  		* 크기가 정해져있으며
  		* 컴파일 할때 이미 계산이 이루어짐
  		* 메소드가 종료되면 메모리 공간은 반환, 지워진다.
  		* **장점 : 메모리 누수 걱정 x, 프로그램 종료시에 알아서 운영체제가 회수**
  		* **단점 : 메모리 크기가 하드 코딩되어 있어서 나중에 조절 불가능, 스택에 할당된 메모리로 동적 할당에 비해 할당 받을 최대 메모리의 제약을 받는다.**
  	* **Heap ( 동적 메모리 할당) 이란?**
  		* **컴퓨터 프로그래밍에서 실행 시간 동안 사용할 메모리 공간 할당하는 것을 말한다**
  		* 사용이 끝나면 OS에 반납하고 다시 요구가 오면 재할당
  		* **명시적으로 쓰레기 수집이 일어나기 전 까지 사용된다.**
  		* 자바는 더 이상 사용하지 않으면 알아서 GC가 메모리를 수거한다. 
  		* **배열, 열거, 클래스 ,인터페이스가 이에 속한다**
  		* **참조타입들을 힙 영역에 주소형식으로 저장한다.**
  		* 참조 변수가 없으면 자동으로 힙 영역에서 제거된다.
  		* **장점 : 상황에 따라 원하는 메모리 할당, 경제적, 크기 조절 가능**
  		* **단점 : 더 이상 사용하지 않을때 명시적으로 제거 해줘야 하지만 JVM이 GC되니까 이 단점을 보안**

  * **OOP의 5대 원칙 ( SOLID )**

    * SRP
    	* 객체는 단 하나의 책임만 가져야 한다
    * OCP
    	* 기존의 코드를 변경하지 않으면서 기능을 추가할 수 있도록 설계가 되어야 한다.
    * LSP
    	* 일반화 관계에 대한 이야기며, 자식 클래스는 최소한 자신의 부모 클래스에서 가능한 행위는 수행 가능하여야 한다.
    * ISP
    	* 인터페이스를 클라이언트에 특화되도록 분리시키라는 설계 원칙
    * DIP
    	* 의존 관계를 맺을 때 변화하기 쉬운 것 또는 자주 변화하는 것 보다는 변화하기 어려운것, 거의 변화가 없는 것에 의존하라는 것이다.

  * **객제지향 vs 절차지향**

    * 절차지향
    	* 실행하고자 하는 절차를 정하고, 이 절차대로 프로그로밍 하는 방법
    	* 목적을 달성하기 위한 일의 흐름에 중점을 둔다.
    * 객체지향
      * 실세상의 물체를 객체로 표현하고, 이들 사이의 관계, 상호 작용을 프로그램으로 나타낸다.
      * 객체를 추출하고 객체들의 관계를 결정하고 이들의 상호 작용에 필요한 함수와 변수를 설계 및 구현한다.
      * 핵심은 변수와 메서드를 하나의 그룹으로 묶어서 그룹핑하는 것이다.
      * 사람의 사고와 비슷하게 프로그래밍 하기 위해 생겨난 기법
      * 하나의 클래스를 바탕으로 서로 다른 상태를 가진 인스턴스를 만들면 서로 다른 행동을 하게 된다.
      * 하나의 클래스가 여러 개의 인스턴스가 될 수 있다는 점이 객체 지향이 제공하는 기본적인 재활용성이라고 한다.
      * **특징**
      	1. 캡슐화
      	2. 정보은닉
      	3. 추상화
      	4. 상속성
      	5. 다형성

  * **non static vs static**

  	* **non-static 멤버**
  		* 공간적 특성 : 멤버는 객체마다 별도로 존재한다.
  			* 인스턴스 멤버라고 부른다.
  		* 시간적 특성 : 객체 생성시에 멤버가 생성된다.
  			* 객체가 생길 때 멤버도 생성된다.
  			* 객체 생성 후 멤버 사용이 가능하다
  			* 객체가 사라지면 멤버도 사라진다.
  		* 공유의 특성 : 공유되지 않는다.
  			* 멤버는 객체 내에 각각의 공간을 유지한다.
  	* **static 멤버**
  		* 공간적 특성 : 멤버는 클래스당 하나가 생성된다.
  			* 멤버는 객체 내부가 아닌 별도의 공간에 생성된다.
  			* 클래스 멤버라고 부른다.
  		* 시간적 특성 : 클래스 로딩 시에 멤버가 생성된다.
  			* 객체가 생기기 전에 이미 생성된다.
  			* 객체가 생기기 전에도 사용이 가능하다 -> 객체를 생성하지 않고도 사용가능
  		* **공유의 특성** : 동일한 클래스의 모든 객체들에 의해 공유된다. 

  * **final 키워드**

  	* **final 키워드**
  		* 개념 : 변수나 메서드 또는 클래스가 **"변경 불가능"**하도록 만든다.
  		* 원시 변수에 적용시 : 변수의 값 변경 불가능
  		* 참조 변수에 적용시 : 참조 변수가 힙 내의 다른 객체를 가리키도록 변경 불가
  		* 메소드에 적용시 : 해당 메서드를 오버라이드 불가능
  		* 클래스에 적용시 : 해당 클래스의 하위 클래스를 정의할 수 없다.
  	* **finally 키워드**
  		* 개념 : try/catch 블록이 종료될때 실행될 코드 블록 정의
  		* 선택적으로 혹은 try/catch 뒤에 사용
  		* 예외 발생해도 실행
  		* 뒷마무리 코드, 통제권이 이전으로 다시 돌아가기전 실행
  	* **finalize() 메서드**
  		* 개념 : GC가 더 이상의 참조가 존재하지 않는 객체를 메모리에서 삭제하겠다고 결정하는 순간 호출
  		* Object 클래스의 `finalize()` 메서드를 오버라이딩해서 맞춤별 GC를 정의 가능

  * **java의 Generic vs C++의 Template**

  	* java Generic
  		* `class Stack<E> {}` 에서의 `E`
  		* **모든 종류의 타입을 다룰 수 있도록 일반화된 타입 매개 변수로 클래스나 메서드 선언**
  		* 타입 제거라는 개념에 근거
  		* 소스 코드를 JVM이 인삭하는 바이트 코드로 변환할 때 인자로 주어진 타입을 제거하는 기술이다.
  	* C++ Template
  		* 하나의 클래스를 서로 다른 여러 타입에 재사용할 수 있도록 하는 방법
  		* 여러 타입의 객체를 저장할 수 있는 연결리스트와 같은 자료구조를 만들 수 있다.
  		* 컴파일러는 인자로 주어진 각각의 타입에 대해 별도의 템플릿 코드를 생성한다.

  * **객체의 직렬화와 역직렬화**

  	* **직렬화**
  		* 객체를 직렬화하여 **전송 가능한 형태로 만드는 것을 의미한다.**
  		* 주로 통째로 파일을 저장하거나 전송하고 싶을 때 사용된다.
  		* `public class A Implements Serializable { ... } `의 형태로 사용한다.
  		* 없으면 보통 불가능하고 상속받은걸 Implements해서 사용은 가능
  		* `private transient String password;`를 통해 제외가능
  	* **역직렬화**
  		* 직렬화된 파일 등을 역으로 직렬화하여 다시 객체의 형태로 만드는 것을 의미한다.
  		* 저장된 파일을 읽거나 전송된 스트림 데이터를 읽어 원래 객체의 형태로 복원한다.
  	* `SerialVersion UID`
  		* 고유의 번호를 생성하여 직렬화, 역직렬화에서 중요한 역할 수행

  * **클래스, 객체, 인스턴스의 차이**

  	* **클래스**

  		* 객체를 만들어 내기 위한 설계도 혹은 틀
  		* 연관되어 있는 변수와 메서드의 집합

  	* **객체**

  		* 소프트웨어 세계에 구현할 대상
  		* 클래스에 선언된 모양 그대로 생성된 실체
  		* 메모리에 할당된 실체화된 인스턴스를 객체라고 부른다.
  		* **클래스의 인스턴스 라고도 부른다**
  		* 객체는 모든 인스턴스를 대표하는 포괄적인 의미
  		* oop의 관점에서 클래스의 타입으로 선언되었을 때 '객체'라고 부른다.

  	* **인스턴스**

  		* 설계도를 바탕으로 소프트웨어 세계에 구현된 구체적인 실체
  		* 즉, 객체를 소프트웨어에 실체화 하면 그것을 **''인스턴스'**'라고 부른다
  		* oop의 관점에서 객체가 메모리에 할당되어 실제로 사용될때 **'인스턴스'**라고 부름 

  		```java
  		// 클래스
  		public class Animal {
  		    ...
  		}
  		
  		public class Main {
  		    public static void main(String [] args){
  		        Animal cat, dog; // 객체
  		        
  		        // 인스턴스 화
  		        cat = new Animal(); // cat은 Animal 클래스의 인스턴스 
  		        dog = new Animal(); // 객체를 메모리에 할당한 순간 
  		    }
  		}
  		```

  	* **클래스 vs 객체**

  		* 클래스는 설계도, 객체는 설계도로 구현한 모든 대상

  	* **객체 vs 인스턴스**

  		* 클래스의 타입으로 선언되었을 때 객체라고 부르고, 그 **객체가 메모리에 할당되어 실제로 사용될 때 인스턴스라고 부른다.**
  		* 객체 : 현실, 실체
  		* 인스턴스 : 소프트웨어, 관계

  * **오버로딩 vs 오버라이딩**

  	* **오버로딩**

  		* 두 메서드가 같은 이름을 갖고 있으나 인자수나 자료형이 다른경우

  		```java
  		public int sort(int []arr){};
  		public int sort(int []arr,int []arr2){}
  		```

  	* **오버라이딩**

  		* 상위 클래스의 메서드와 이름과 용례가 같은 함수를 하위 클래스에 재정의하는 것
  		* 상속 관계에 있는 클래스 간에 같은 이름의 메서드를 정의

  * **Call by Reference vs Call by Value**

  	* **Call by Value**
  		* 함수가 호출될 때, 메모리 공간 안에서는 함수를 위한 별도의 임시 공간이 생성된다.
  		* 함수 호출시 인자로 전달되는 변수의 값을 복사하여 함수의 인자로 전달한다.
  		* 복사된 인자는 함수 안에서 지역적으로 사용되는 local value의 특성을 가진다.
  		* 따라서 함수 안에서 인자의 값이 변경되어도, 외부의 변수의 값은 변경되지 않는다.
  	* **Call by Reference**
  		* 함수가 호출될 때, 메모리 공간 안에서는 함수를 위한 별도의 임시 공간이 생성된다.
  		* 함수 호출시 인자로 전달되는 변수의 레퍼런스를 전달한다.
  		* 따라서 함수 안에서 인자의 값이 변경되면, 인자로 전달된 변수의 값도 함께 변경된다.
  	* **java는 항상 Call by Value**이다
  	* **대표 예시 http://wonwoo.ml/index.php/post/1679**

  * **인터페이스 vs 추상 클래스**

  	* 추상 메서드
  		* 개념 : `abstract` 키워드와 함께 원형만 선언되고, 코드는 작성되지 않은 메서드
  			* 추상 메서드를 최소 한 개 이상 가지고 `abstract`로 선언된 클래스
  			* 추상 메서드가 없어도 `abstract` 선언한 클래스
  		* 구현 
  			* 서브 클래스에서 슈퍼 클래스의 모든 추상 메서드를 오버라이딩하여 실행가능한 코드로 구현한다
  		* 목적
  			* 객체를 생성하기 위함이 아니며, 상속을 위한 부모 클래스로 활용하기 위한 것이다.
  			* 여러 클래스들의 공통된 부분을 추상화하여 상속받은 클래스에게 구현을 강제화하기 위한 것이다.
  	* **인터페이스**
  		* 개념 : 추상 메서드와 상수만을 포함하며, `interface` 키워드를 사용하여 선언한다.
  		* 구현
  			* 인터페이스를 상속받고, 추상 메서드를 모두 구현한 클래스를 작성한다.
  			* `implements` 키워드를 사용하여 구현한다.
  		* 목적
  			* 상속받을 서브 클래스에게 구현할 메서드들의 우너형을 모두 알려주어, 클래스가 자신의 목적에 맞게 메서드를 구현하도록 하는 것이다.
  			* 구현 객체의 같은 동작을 보장하기 위한 목적이 있다.
  			* 기능을 각각 구현할 필요가 있는 경우에 사용한다.
  		* 특징
  			* 인터페이스는 상수 필드와 추상 메서드만으로 구성된다.
  			* 모든 메서드는 추상 메서드로서, `abstract public`속성이며 생략가능
  			* 상수는 `public static final`속성이며 생략가능
  			* 인터페이스를 상속받아 새로운 인터페이스를 만들 수 있다.
  	* **공통점**
  		* 인스턴스는 생성할 수 없다.
  		* 선언만 있고 구현 내용이 없다
  		* 자식 클래스가 메서드의 구체적인 동작을 구현하도록 책임을 위임한다.
  	* **차이점**
  		* 서로 다른 목적을 가지고 있다.
  			* 추상 클래스는 추상 메서드를 자식 클래스가 구체화하여 그 기능을 확장시키는 목적
  			* 인터페이스는 서로 관련이 없는 클래스에서 공통적으로 사용하는 방식이 필요하지만 기능을 각각 구현할 필요가 있는 경우에 사용한다.
  		* 추상 클래스는 클래스지만 인터페이스는 아니다
  		* 추상 클래스는 단일 상속
  		* 인터페이스는 다중 상속이 가능하다
  		* 추상 클래스 **is a kind of**
  		* 인터페이스 **can do this**

  * **JVM 구조**

  	* **Java Collections Framework**
  	* ![img](https://gmlwjd9405.github.io/images/basic-concepts-of-development/java-collections-framework.png)
  		* Map
  			* 검색할 수 있는 인터페이스
  			* 데이터를 삽입할 때 key value 형태로 삽입 해서 얻어 쓴다.
  		* Collection
  			* List
  				* 순서가 있음
  				* 데이터 중복 가능
  			* set
  				* 집합적인 개념
  				* 순서 의미가 없고
  				* 데이터 중복 못함
  		* Map 구현체 종류
  			* HashMap
  				* `Entry<K,V>`의 배열로 저장되며, 배열의 index는 내부 해쉬 함수를 통해 계산된다.
  				* 특정 규칙 없이 출력
  				* null값 허용
  				* 비동기 처리
  				* 시간복잡도 O(1)
  			* LinkedHashMap
  				* HaspMap 상속받은 Linked List
  				* 입력 순서대로 출력
  				* 비동기
  				* O(n)
  			* TreeMap
  				* 내부적인 레드-블랙 트리로 저장
  				* 오름차순
  				* O(logn)
  		* Set 인터페이스 구현체의 종류
  			* HashSet
  				* **저장 순서를 유지하지 않는 데이터 집합**
  				* 해시 알고리즘을 사용하여 **검색 속도가 매우 빠르다**
  				* **내부적으로 요소 저장**
  		* List 인터페이스 구현체의 종류
  			* ArrayList
  				* **단방향 포인터 구조로 각 데이터에 대한 인덱스를 가지고 있어 검색에 적합**
  				* **crud가 빈번한 데이터는 부적합**
  			* LinkedList
  				* 양방향 포인터 구조로 데이터 **crud 적합**
  				* **검색에는 부적합**
  				* **스택, 큐, 양방향 큐 등을 만들때 사용**
  			* Vector
  				* 내부에서 자동으로 동기화 처리가 일어난다
  				* 성능이 좋지 않고 무거워 잘 안씀

  * `String, StringBuilder, StringBuffer`

  	* String
  		* 새로운 값을 할당할 때마다 새로 클래스에 대한 객체 생성
  		* String에서 저장되는 문자열은 `private final char[]`의 형태이기 때문에 String값은 바꿀수 없다.
  		* `String+String+String`
  			* **각각의 String 주솟값이 Stack에 쌓이고, Garbage Collector가 호출되기 전까지 생성된 String 객체들은 Heap에 쌓이기 때문에 메모리 관리에 치명적이다.**
  		* 직접 더하는거보다 **StringBuffer나 StringBuilder를 사용하는게 좋다**
  	* `StringBuilder, StringBuffer`
  		* memory에 append하는 방식으로 클래스에 대한 **객체 직접 생성하지 않는다.**
  			* `StringBuilder`
  				* 변경가능한 문자열
  				* **비동기 처리**
  			* `StringBuffer`
  				* 변경가능한 문자열
  				* **동기 처리**
  				* multiple thread 환경에서 안전한 클래스

  * Java에서 `==` 과 `Equals()`

  	* 공통점

  		* 양쪽에 값을 비교 후 `boolean`타입을 리턴한다.

  	* 차이점

  		* 형태의 차이
  			* 메소드 vs 연산자
  		* 주소값 비교와 내용 비교
  			* `equals` 메소드는 비교하고자 하는 **대상의 내용 자체를 비교**하지만
  			* `==`연산자는 비교하고자 하는 대상의 **주소값을 비교**합니다.

  	* **npe 유발코드**

  		```java
  		String a = null;
  		System.out.println("ddd".equals(a));
  		System.out.println(a.equals("ddd"));// npe 유발코드 
  		a = "ddd";
  		```

  	* **두번째 출력은 오류가 난다**

  * **동기화 vs 비동기화**

  	