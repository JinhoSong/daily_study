### 정보처리기사

---

1. **정보시스템 구축 관리**
	1. **IT 프로젝트 정보 시스템 구축 관리**
		1. [네트워크 관련 신기술](#네트워크-관련-신기술)
		2. [네트워크 구축](#네트워크-구축)
		3. [스위치](#스위치)
		4. [경로 제어 / 트래픽 제어](#경로-제어-/-트래픽-제어)
		5. [SW 관련 신기술](#SW-관련-신기술)
		6. [소프트웨어 개발 보안](#소프트웨어-개발-보안)
		7. [소프트웨어 개발 직무별 보안 활동](#소프트웨어-개발-직무별-보안-활동)
		8. [소프트웨어 개발 보안 활동 관련 법령 및 규정](#소프트웨어-개발-보안-활동-관련-법령-및-규정)
		9. [HW 관련 신기술](#-관련-신기술)
		10. [DB 관련 신기술](#DB-관련-신기술)
		11. [회복 / 병행제어](#회복-/-병행제어)
		12. [데이터 표준화](#데이터-표준화)

---

1. ### 네트워크 관련 신기술

	1. **IoT**

		* 정보 통신 기술을 기반으로 실세계와 가상 세계의 다양한 사물과 사람을 인터넷으로 서로 연결하여 진보된 서비스를 제공하기 위한 서비스 기반 기술
		* **유비쿼터스** 공간을 구현하기 위해 M2M개념으로 확장

	2. **M2M**

		* **무선 통신을 이용한 기계와 기계 사이의 통신**
		* 변압기 원격 감시, 전기 ,가스 원격 검침, CDMA, GSM 다양한 무선통신망

	3. **Mobile Computing**

		* 휴대형 기기로 이동하면서 자유롭게 네트워크에 접속하여 업무를 처리할 수 있는 환경
		* 개인의 삶의 질 향상 가능

	4. **Cloud Computing**

		* 각종 컴퓨터 자원을 중앙 컴퓨터에 두고 인터넷 기능을 갖는 단말기로 언제 어디서나 인터넷을 통해 컴퓨터 작업을 수행할 수 있는 환경
		* **Grid 컴퓨터랑은 다르다**
		* **중앙의 대형 데이터 센터의 컴퓨팅 자원을 필요한 이들에게 필요한 순간 빌려주는 형태**

	5. **MCC ( Mobile Cloud Computing)**

		* 모바일과 클라우드 컴퓨팅 혼합하여 클라우드 서비스를 이용하여 모바일 기기로 클라우딩 컴퓨팅 인프라를 구성하여 여러 가지 정보와 자원을 공유하는 ICT 기술을 의미
		* **ICT** : 정보기술과 통신기술을 합한 말
		* **OS등과 같은 환경에 구애 받지 않는다.**

	6. **Inter Cloud Computing**

		* 각기 다른 클라우드 서비스를 연동하거나 컴퓨팅 자원의 동적 할당이 가능하도록 여러 클라우드 서비스 제공자들이 제공하는 클라우드 서비스나 자원을 연결하는 기술

		1. **대등 접속 (Peering)** : 클라우드 서비스 제공자 간 직접 연계하는 형태
		2. **연합 (Federation)** : 자원 공유를 기본으로 사용 요구량에 따른 동적 자우너 할당을 지원함으로써 논리적으로 하나의 서비스를 제공하는 형태
		3. **중개 (Intermediary)** : 서비스 제공자 간의 직간접적인 자원 연계 또는 단일 서비스 제공자를 통한 중개 서비스를 제공하는 형태

	7. **Mesh Network**

		* 차세데 이동통신, 홈네트워킹, 공공 안전 등 특수 목적을 위한 새로운 방식의 네트워크 기술, 대규모 네트워크 생성에 특화
		* **무선 랜의 한계 극복을 위해 라우터들을 기지국으로 활용하여 무선망처럼 사용**

	8. **Wi-SUN**

		* 장거리 무선 통신을 필요로 하는 사물인터넷 서비스를 위한 저전력 장거리 통신 기술
		* 짧은 시간 동안 데이터 전송이 빈번한 검침 분야에 유용

	9. **NDN (Named Data Networking)**

		* 콘텐츠 자체의 정보와 라우터 기능만으로 데이터 전송을 수행하는 기술
		* 기존의 IP망을 대체할 새로운 인터넷 아키텍처로 떠오름
		* **CCN (Content Centric networking)** : 인터넷에서 IP주소에 따른 데이터 전송에서 벗어나 사용자가 요구하는 콘텐츠 중심의 데이터 전달이 가능한 네트워크
		* **콘텐츠에 담겨 있는 정보와 라우터 기능만으로 목적지를 확정한다**

	10. **NGN (Next Generation Network, 차세데 통신망)**

		* 유선망 기반의 차세대 통신망으로 유선망 뿐만 아니라 이동 사용자를 목표로 하며 이동 통신에서 제공하는 완전한 이동성 제공을 목표로 개발

	11. **SDN ( Software Defined Networking, 소프트웨어 정의 네트워킹)**

		* 네트워크를 컴퓨터처럼 모델링하여 여러 사용자가 각각의 소프트웨들로 네트워킹을 가상화하여 제어하고 관리하는 네트워크
		* 네트워크 비용 및 복잡성 해결을 위한 미래 기술로 떠오르는 중

	12. **NFC ( Near Field Communication, 근거리 무선 통신)**

		* 고주파를 이용한 근거리 무선 통신 기술
		* **ISO/IEC 18092표준**

	13. **UWB ( Ultra WideBand, 초광대역)**

		* 짧은 거리에서 많은 양의 디지털 데이터를 낮은 전력으로 전송하기 위한 무선 기술로 무선 디지털 펄스라고도 함

	14. **피코넷 PICONET**

		* 여러 개의 독립된 통신 장치가 블루투스 기술이나 UWB 통신 기술을 사용하여 통신망을 형성하는 무선 네트워크 기술

	15. **WBAN ( Wireless Body Area Network)**

		* **Wearable 또는 Implant 형태의 센서나 기기를 무선으로 연결하는 네트워킹 기술**

	16. **GIS ( Geographic Information System, 지리 정보 시스템)**

		* 지리적인 자료를 수집, 저장, 분석, 출력할 수 있는 컴퓨터 응용 시스템으로 위성을 이용해 모든 사물의 위치 정보를 제공해줌

	17. **USN ( Ubiquitous Sensor Network)**

		* 각종 센서로 수집한 정보를 무선으로 수집할 수 있도록 구성한 네트워크를 말한다.
		* 필요한 모든곳에 **RFID 태그를 부착**

	18. **SON ( Self Organizing Network)**

		* 주변 상황에 맞추어 스스로 망을 구성하는 네트워크

	19. **Ad-hoc Network**

		* 재난 현장과 같이 별도의 고정된 유선망을 구축할 수 없는 장소에서 모바일 호스트만으로 이용하여 구성한 네트워크

	20. **네트워크 슬라이싱**

		* 여러 글로벌 이동통신 표준화 단체가 선정한 5G의 핵심기술 중 하나로 네트워크에서 하나의 물리적인 코어 네트워크 인프라를 독립된 다수의 가상 네트워크로 분리하여 각각의 네트워크를 통해 다양한 고객 맞춤형 서비스를 제공하는 것을 목적으로 하는 네트워크 기술
		* **SDN, NFV 등이 있다**

	21. **저전력 블루투스 기술**

		* 일반 블루투스와 동일한 주파수 대역을 사용하지만 연결되지 않은 상태에서는 절전 모드를 유지하는 기술

	22. **지능형 초연결망**

		* 스마트 시티, 스마트 스테이션 등 4차 산업혁명 시대를 맞아 새로운 변화에 따라 급격하게 증가하는 데이터 트래픽을 효과적으로 수용하기 위해 시행되는 정부 주관 사업

2. ### 네트워크 구축

  1. **네트워크 설치 구조**
  	* 정보를 전달하기 위해 통신 규약에 의해 연결한 통신 설비의 집합
  2. **성형 ( Star, 중앙 집중형)**
  	* 중앙 컴퓨터에 단말 장치들이 연결되는 구조
  	* Point-to-Point 방식으로 연결
  	* 중앙 집중식이므로 교환 노드의 수가 가장 적음
  	* 단말장치가 고장나도 전체에 영향x
  	* 중앙이 고장나면 전체 영향
  3. **링형 (Ring, 루프형)**
  	* 컴퓨터와 단말장치들을 서로 이웃하는 것끼리 Point-to-Point연결
  	* 데이터는 단방향 또는 양방향 가능
  	* 하나의 단말이 고장나면 전체에 영향을 줌
  	* **주로 LAN**
  4. **버스형**
  	* 한 개의 통신 회선에 여러 대의 단말 장치가 연결되어 있는 구조 
  	* 단말이 고장나도 영향x
  	* **주로 LAN**
  5. **계층형 ( Tree, 분산형)**
  	* 중앙 컴퓨터와 일정 지역의 단말 장치까지는 하나의 회선으로 연결시키고 이웃하는 단말장치는 일정 지역 내 설치된 중간 단말장치로부터 다시 연결
  	* 분산처리 시스템을 구성하는 방식
  6. **망형(Mesh)**
  	* 모든 지점의 컴퓨터와 단말이 서로 연결
  	* 많은 양의 통신이 필요한 경우 사용
  	* 필요한 포트의 수 = n -1
  	* 필요한 회선의 수 = n(n-1)
  7. **네트워크 분류**
  	* **근거리 통신망 LAN**
  		1. 비교적 가까운 거리에 있는 노드들을 연결하여 구성
  		2. 주로 버스형이나 링형 구조 사용
  	* **광대역 통신망 WAN**
  		1. 국가와 국가, 대륙과 대륙 등 멀리 떨어진 사이트들을 연결하여 구성
  		2. 일정 지역은 LAN으로하고 LAN을 연결하는 방식을 사용

3. ### 스위치

  1. **스위치의 정의**

  	* LAN과 LAN을 연결하여 훨씬 더 큰 LAN을 만드는 장치
  	
   2. **스위치의 분류**
       
       | 스위치 종류 |                             특징                             |
       | :---------: | :----------------------------------------------------------: |
       |  L2 스위치  | OSI 2계층에 속함<br />일반적으로 부르는 스위치를 말함<br />**MAC주소를 기반으로 프레임 전송**<br />동일한 네트워크 간 연결만 가능 |
       |  L3 스위치  | OSI 3계층<br />L2 스위치에 라우터 기능을 추가<br />**ip주소를 기반으로 패킷 전송**<br />서로 다른 네트워크 연결 가능 |
       |  L4 스위치  | OSI 4계층<br />L3 스위치에 로드밸런스 추가<br />IP주소 및 TCP/UDP를 기반으로 사용자들의 요구를 서버의 부하가 적은 곳으로 배분하는 로드 밸런싱기능 |
       |  L7 스위치  | OSI 7계층<br />IP주소, TCP/UDP 포트 정보에 패킷 내용까지 참조하여 세밀하게 로드밸런싱 함 |
       
  3. **스위칭 방식**

       * 스위치가 프레임을 전달하는 방식을 기준으로 나뉨

       1. **Store and Forwarding** : 데이터를 모두 받은 후 스위칭
       2. **Cut-through** : 데이터의 목적지 주소만 확인 후 바로 스위칭
       3. **Fragment Free** : 위의 두 방식의 장점만을 결합한 방식

  4. **백본 스위치**

       * 여러 네트워크들을 연결할 때 중추적 역할을 하는 네트워크인 백본에서 스위칭하는 장비
       * 모든 패킷이 지나가는 네트워크 중심에 배치
       * 주로 L3스위치가 백본 스위치 역할을 함

  5. **Hierarchical 3 Layer 모델**

       1. **엑세스 계층**
       	* PC, WEB SERVER, L2 스위치
       2. **디스트리뷰션 계층**
       	* L3스위치
       3. **코어 계층**
       	* 백본 스위치

4. ### 경로 / 트래픽 제어

	1. **경로 제어의 개요**

		* **전송 경로 중 어느 한 경로에 데이터의 양이 집중되는 것을 피하면서 최저의 비용으로 최단 시간에 송신할 수 있는 경로인 최적 패킷 교환 경로를 결정**
		* 경로 제어표를 참조해서 라우터에 의해 수행
		* 경로 제어 요소
			1. 성능 기준
			2. 경로의 결정 시간
			3. 장소, 정보 발생지
			4. 경로 정보의 갱신 시간

	2. **경로 제어 프로토콜**

		* 효율적인 경로 제어를 위해 네트워크를 제어하는 프로토콜
		* **IGP 내부 게이트웨이 프로토콜**
			1. 하나의 자율 시스템 내의 라우팅에 사용
			2. **RIP** : 현재 가장 널리 사용되며 소규모 네트워크에서 효율적인 방법
			3. **OSPF** : 대규모 네트워크에서 많이 사용되는 프로토콜
		* **EGP**
			* 자율 시스템/ 게이트웨이 간 라우팅에 사용되는 프로토콜
		* **BGP**
			* 자율 시스템 간의 라우팅 프로토콜
			* **EGP의 단점 보완**
			* 초기 연결 시 라우팅 테이블을 교환하고 이후에는 변화된 정보만을 교환

	3. **트래픽 제어의 개요**

		* 네트워크의 보호, 성능 유지, 자원의 효율적인 이용을 위해 전송되는 패킷의 흐름, 양을 조절하는 기능

	4. **흐름 제어**

		* 네트워크 내의 원활한 흐름을 위해 송수신 사이에 전송되는 패킷의 양이나 속도를 규제
		* 송수시간 처리 속도 또는 버퍼 크기의 차이에 의해 생길 수 있는 버퍼 오버플로우를 방지
		* 정지-대기 : 수신 측에서 확인 신호를 받아야 패킷 전송, 한번에 하나만 전송 가능 
		* **슬라이딩 윈도우**
			1. 확인 신호를 이용한 송신 데이터의 양 조절
			2. 수신 측의 확인 신호를 받지 않아도 정해진 패킷 수 만큼 연속적으로 전송하는 방식
			3. 한 번에 여러 개의 패킷을 전송 가능

	5. **혼잡 제어**

		* 네트워크 내의 패킷 수를 조절하여 네트워크 오버플로우를 방지

		1. **느린 시작** : 윈도우 크기를  1,2,4,8로 늘려나가다가 임계 값 도달하면 혼잡 회피로 넘어감
		2. **혼잡 회피** : 지수적 증가가 임계 값에 도달하면 혼잡으로 간주하고 회피를 위해 윈도우의 크기를 1씩 선형적으로 증가시켜 혼잡을 예방 

	6. **교착상태 방지**

		* 교환기 내에 패킷들을 축적하는 기억 공간이 꽉 차 있을 때 다음 패킷들을 이 기억 공간에 들어가기 위해 무한정 기다리는 현상을 방지 

5. ### SW 관련 신기술

	1. **인공지능**
		* 인간의 두뇌 같이 컴퓨터 스스로 추론, 학습, 판단 등 인간 지능적인 작업을 수행하는 시스템
		* **LISP, PROLOG등이 있다.**
	2. **뉴럴링크**
		* 테슬라의 CEO 일론 머스크가 사람의 뇌와 컴퓨터와 결합하는 기술을 개발하기 위해 설립한 회사
	3. **Deep Learning**
		* 인간의 두뇌를 모델로 만들어진 인공 신경망을 기반으로 학습하는 기술 ANN
	4. **전문가 시스템**
		* 특정 분야의 전문가가 수행하는 고도의 업무를 지원하기 위한 컴퓨터 응용 프록르ㅐㅁ
	5. **증강현실 AR**
		* 실제 촬영한 화면에 가상의 정보를 부가하여 보여주는 기술
	6. **블록체인**
		* p2p 네트워크를 이용하여 온라인 금융 거래 정보를 온라인 네트워크 Peer의 디지털 장비에 분산 저장하는 기술
	7. **분산 원장 기술**
		* 중앙 관리자나 중앙 데이터 저장소가 존재하지 않고 P2P 망내의 참여자들에게 모든 거래 목록이 분산되어 거래가 발생할 때마다 지속적으로 갱신되는 디지털 원장
		* 대표사례로 블록체인이 있다.
	8. **해시**
		* 임의의 길이의 입력 데이터나 메시지를 고정딘 길이의 값이나 키로 변환
	9. **양자 암호키 분배 QKD**
		* 양자 통신을 위해 비밀키를 분배하여 관리하는 기술
	10. **프라이버시 강화 기술 PET**
		* 개인정보 위험 관리 기술이다.
	11. **디지털 저작권 관리 DRM**
		* 저작권 보호를 위해 데이터의 안전한 배포를 활성화하거나 불법 배포를 방지하기 위한 시스템
	12. **공통 평가 기준 CC**
		* ISO 15408표준으로 채택
		* **정보화 순기능 역할을 보장하기 위해 정보화 제품의 정보보호 기능과 이에 대한 사용 환경 등급을 정한 기준 **
	13. **개인정보 영향 평가 제도**
		* 개인정보를 활용하는 새로운 정보 시스템의 도입 및 기존 정보시스템의 중요한 변경 시 시스템의 구축, 운영이 기업의 고객과 물론 국민의 사생활에 미칠 영향에 대해 미리 조사, 분석, 평가하는 제도
	14. **그레이웨어**
		* 소프트웨어를 제공하는 입장에서는 악의적이지 않은 유용한 소프트웨어 일지라도 사용자 입장에서는 유용할 수도 있고 악의적일 수도 있는 악성 코드나 공유 웨어
	15. **Mashup**
		* **웹에서 제공하는 정보 및 서비스를 이용하여 새로운 소프트웨어나 서비스 데이터베이스 등을 만드는 기술**
	16. **리치 인터넷 애플리케이션**
		* 플래시 애니메이션 기술과 웹 서버 애플리케이션 기술을 통합하여 기존 HTML보다 역동적이고 인터랙티브한 웹 페이지를 제공하는 제작 기술
		* RIA는 다양한 컴포넌트가 추가된 Flash와 Flex 같은 멀티미디어 도구와 DB가 연동되는 단일 인터페이스를 통해 기존의 웹에서는 볼 수 없었던 다이나믹하고 편리한 고객 중심의 웹페이지를 제공한다.
	17. **시맨틱 웹**
		* **컴퓨터가 사람을 대신하여 정보를 읽고 이해하고 가공하여 새로운 정보를 만들어 낼 수 있도록 이해하기 쉬운 의미를 가진 차세대 지능형 웹**
	18. **증발품**
		* 판매 계획 또는 배포 계획은 발표되었으나 실제로 고객에게는 판매되거나 배포하지 않고 있는 소프트웨어
	19. **오픈 그리드 서비스 아키텍처 OGSA**
		* **애플리케이션 공유를 위한 웹 서비스를 그리드 상에 제공하기 위해 만든 개방형 표준**
	20. **서비스 지향 아키텍처 SOA**
		* **기업의 소프트웨어 인프라인 정보시스템 공유와 재사용이 가능한 서비스 단위나 컴포넌트 중심으로 구축하는 정보기술 아키텍처**
	21. **서비스형 소프트웨어 SaaS**
		* 소프트웨어의 여러 기능 중에서 사용자가 필요로 하는 서비스만 이용할 수 있도록 한 소프트웨어
	22. **소프트웨어 에스크로 (임치)**
		* 소프트웨어 개발자의 지식재산권을 보호하고 사용자는 저렴한 비용으로 소프트웨어를 안정적으로 사용 및 유지보수 받을 수 있도록 소스 프로그램과 기술 정보 등을 제3의 기관에 보관하는 것
	23. **복잡 이벤트 처리**
		* 실시간으로 발생하는 많은 사건들 중 의미가 있는 것만을 추출할 수 있도록 사건 발생 조건을 정의하는 데이터 처리 방법
	24. **디지털 트윈**
		* 현실속의 사물을 소프트웨어로 가상화한 모델

6. ### 소프트웨어 개발 보안

	1. **소프트웨어 개발 보안**

		* 소프트웨어 개발 과정에서 발생할 수 있는 보안 취약점을 최소화하여 보안 위협으로부터 안전한 소프트웨어를 개발 하기 위한 보안 활동
		* 데이터의 기밀성, 무결성, 가용성 유지

	2. **소프트웨어 개발 보안 관련 기관**

		1. 행정안전부 ( 정책기관 )
			* 개발 보안 정책을 총괄
			* 법규, 지침, 제도 정비
			* 약점 진단하는 사람을 양성
		2. 한국인터넷 진흥원 **KISA** ( 전문 기관)
			* 개발 정책 및 가이드를 개발 
			* 개발 보안에 대한 기술을 지원하고 교육 과정 및 자격제도 운영
		3. 발주기관
			* 개발 보안의 계획을 수립
			* 사업자 및 감리법인을 선정
		4. 사업자
			* 적용 계획을 명시
			* 인력 대상 교육
			* 참조하여 개발
			* 약점 진단 제거
			* 요구사항 이행
		5. 감리법인 ( 소프트웨어의 보안 약점 진단)
			* 감리 계획을 수립하고 협의
			* 약점 제거 여부 및 조치를 결과 확인 

		* **감 사 한 발 행**

7. ### 소프트웨어 개발 직무별 보안 활동

	1. **프로젝트 관리자**
		* 보안 전략을 조직 구성원에게 전달하고 모니터링
	2. **요구사항 분석가**
		* 아키텍트가 고려해야 할 보안 관련 비즈니스 요구사항을 설명
	3. **아키텍트**
		* 보안 오류가 발생하지 않도록 보안 기술 문제를 충분히 이해한다.
		* 시스템에 사용되는 모든 리소스 정의 및 각 리소스별로 적절한 보안 요구사항을 적용한다.
	4. **설계자**
		* 특정 기술에 대해 보안 요구사항의 만족성 여부를 파악하고 소프트웨어에서 발견된 보안 위협에 대해 적절히 대응
		* **문제 발생 시 최선의 문제 해결 방법을 결정**
		* **애플리케이션 보안 수준에 대한 품질 측정을 지원해야 한다**
	5. **구현 개발자**
		* 개발 환경에서 프로그램을 구현할 수 있도록 **시큐어 코딩 표준을 준수하여 개발**
		* **다른 사람이 여부확인 가능하게 문서화**
	6. **테스트 분석가**
		* 소프트웨어 개발 요구사항과 구현 결과 반복 확인
		* 전문가일 필요 없지만 툴사용법 정도는 알아야됨
	7. **보안 감시자**
		* 개발 프로젝트의 전체 단계에서 활동하며 현재 상태와 보안을 보장

8. ### 소프트웨어 개발 보안 활동 관련 법령 및 규정

	1. **개인정보 보호 관련 법령**
		* **개인정보 보호법**
	2. **IT기술 관련 규정**
		* 그냥 책 한번 읽고가면 됨

9. ### HW 관련 신기술

	1. **고가용성 (HA, High Availablity)**
		* 긴 시간동안 안정적인 운영을 위해 장애 즉시 다른 시스템으로 대체 가능한 환경을 구축하는 메커니즘
	2. **3D Printing**
		* 평면에 출력하는 것이 아닌 얇은 두께로 한층 한층 적재시켜 하나의 형태를 만들어내는 기술
	3. **4D Printing**
		* 특정 시간이나 환경 조건이 갖추어지면 스스로 형태를 변화시키거나 제조되는 자가 조립 기술이 적용된 제품을 3D프린팅 하는 기술
	4. **RAID**
		* 여러 개의 하드디스크로 디스크 배열을 구성하여 파일을 구성하고 있는 데이터 블록들을 서로 다른 디스크들에 분산 저장하는 기술
	5. **4k 해상도**
	6. **N-Screen**
		* **N개의 서로 다른 단말기에서 동일한 콘테느를 자유롭게 이용할 수 있는 서비스**
		* 스마트폰, 테플릿, 컴퓨터 등 여러개를 동시에
	7. **Companion Screen**
		* TV방송 시청 시 방송 내용을 공유하며 추가적인 기능을 수행할 수 있는 디바이스
	8. **Thin Client PC**
		* 하드디스크나 주변 장치 없이 기본적인 메모리만 갖추고 서버와 네트워크로 운용되는 개인용 컴퓨터
	9. **Phablet**
		* 폰과 테블릿의 합성어
		* 테블릿 기능을 포함한 **5인치 이상**의 대화면 스마트폰
	10. **C형 유에스비**
		* 더 편리해진 유에시브
	11. **MEMS**
		* 초정밀 반도체 기술을 바탕으로 센서나 액추에이터 등 기계 구조를 다양한 기술로 미세 가공하여 전기 기계적 동작을 할 수 있도록 한 초미세 장치
	12. **트러스트존 기술**
		* ARM에서 개발한 하나의 프로세서 내에 일반 애플리케이션을 처리하는 일반 구역과 보안이 필요한 애플리케이션을 처리하는 보안 구역으로 분할하여 관리하는 하드웨어 기반의 보안 기술
	13. **M-DISC**
		* **한 번의 기록만으로도 자료를 영구 보관할 수 있는 광 저장 장치**
	14. **Memristor**
		* 메모리와 레지스터의 합성어로 전류의 방향과 양 등 기존의 경험을 모두 기억하는 소자

10. ### Secure OS

	1. **Secure OS의 개요**

		* 기존 운영체제에 내재된 보안 취약점을 해소하기 위해 보안 기능을 갖춘 커널을 이식한 운영체제
		* TCB를 기반으로 참조 모니터의 개념을 구현하고 집행
		* 보호 대상 : 메모리, 보조 기억장치 및 저장된 데이터, 하드웨어 장치, 자료 구조, 명령어, 각종 보호 메커니즘 등

		* **보호 방법 복잡한 것부터 차례로 분류**
			1. **암호적 분리** : 내부정보를 암호화
			2. **논리적 분리** : 프로세스의 논리적 구역을 지정하여 구역을 벗어나는 행위를 제한
			3. **시간적 분리** : 동일 시간에 하나의 프로세스만 수행되도록 하여 동일 실행으로 발생하는 보안 취약점을 제거
			4. **물리적 분리** : 사용자별로 특정 장비만 사용하도록 제한

	2. **참조 모니터**

		* 참조 모니터는 보호대상의 객체에 대한 접근통제를 수행하는 추상머신이며, 이것을 실제로 구현한 것이 보안 커널입니다.
		* 참조 모니터는 보안 커널 DB를 참조하여 객체에 대한 접근 허가 여부를 결정합니다.

		* 특징
			1. **Isolation** : 부정 조작이 불가능해야 합니다.
			2. **Verifiablity** : 적절히 구현되었는지 확인 가능해야 합니다.
			3. **Completeness** : 우회가 불가능해야 합니다.

	3. **Seruce OS의 보안 기능**

		* 식별 및 인증, 임의적 접근통제, 강제적 접근 통제, 객체 재사용 보호, 완전한 조정, 신뢰 경로, 감사 및 감사기록 축소

11. ### DB 관련 신기술

	1. **빅데이터**
		* 기존의 관리 방법이나 분석 체계로는 처리하기 어려운 막대한 양의 정형 / 비정형 데이터 집합
	2. **브로드 데이터**
		* 다양한 채널에서 소비자와 상호 작용을 통해 생산 되었거나 기업 마케팅에 있어 효율적이고 다양한 데이터, 이전에 사용하지 않거나 몰랐던 새로운 데이터나 기존 데이터에 새로운 가치가 더해진 데이터
	3. **메타 데이터**
		* 일련의 데이터를 정의하고 설명해주는 데이터
	4. **디지털 아카이빙**
		* 디지털 정보 자원을 정기적으로 보존하기 위한 작업
	5. **하둡**
		* 오픈소스를 기반으로 한 분산 컴퓨팅 플랫폼
	6. **타조**
		* **우리나라가 주도적으로 개발 중인 하둡 기반의 분산 데이터 웨어하우스 프로젝트**
	7. **데이터 다이어트**
		* 데이터를 삭제하는 것이 아닌 압축하고 중복된 정보를 배제하고 새로운 기준에 따라 나누어 저장하는 작업 

12. ### 회복 / 병행제어

	1. **회복**

		* 트랜잭션을 수행하는 도중 장애가 발생하여 데이터베이스가 손상되었을 때 복구하는 작업
		* **장애의 유형**
			1. **트랜잭션 장애**
				* 입력 데이터 오류, 불명확한 데이터
			2. **시스템 장애**
				* DB에 손상을 입히지는 않으나 하드웨어 오동작, SW 손상, 교착상태 등
			3. **미디어 장애**
				* 저장장치인 디스크 블록의 손상이나 디스크 헤드의 충돌 등에 의해 DB일부 또는 전부 물리적으로 손상된 상태
		* **회복 관리기** : 트랜잭션이 실행이 완료되지 못하면 Undo 시키고 이전의 원래 상태로 복구하는 역할을 담당

	2. **병행 제어**

		* 동시에 여러 개의 트랜잭션을 수행할 때 데이터베이스의 일관성을 유지할 수 있도록 트랜잭션 간 상호작용을 제어
		* **병행 제어의 목적**
			1. 데이터베이스의 공유 및 시스템의 활용도 최대화
			2. 데이터베이스 일관성 유지
			3. 응답 시간 최소화

	3. **병행 제어의 문제점**

		|           문제점           |                             의미                             |
		| :------------------------: | :----------------------------------------------------------: |
		|      **Lost update**       | 두 개 이상의 트랜잭션이 같은 자료를 공유하여 갱신할 때 갱신 결과의 일부가 없어짐 |
		| **Uncommitted Dependency** | 하나의 트랜잭션이 실패한 후 회복되기 전에 다른 트<br />**임시갱신 이라고도 한다** |
		|     **Inconsistency**      | 병행 수행될 때 원치 않는 자료를 이용하여 문제 발생<br />**불일치 분석이라고도 함** |
		|   **Cascading Rollback**   | 트랜잭션 중 하나에 문제가 생겨 ROLLBACK 하는 경우 다른 트랜잭션도 같이 ROLLBACK됨 |

13. ### 데이터 표준화

	1. **데이터 표준화의 정의**

		* 시스템을 구성하는데 데이터 요소의 명칭, 정의, 형식, 규칙에 대한 원칙을 수립하고 정의

	2. **데이터 표준**

		* 데이터 모델이나 데이터베이스에서 정의할 수 있는 모든 오브젝트를 대상으로 수행

		1. **표준단어** : 업무에서 사용하는 일정한 의미를 가진 최소 단위의 단어
		2. **표준 도메인** : 칼럼에 성질에 따라 그룹핑함
		3. **표준 코드** : 선택할 수 잇는 값을 기준에 맞게 이미 정의된 코드값
		4. **표준 용어** : 표준 단어 / 도메인 / 코드를 바탕으로 표준 용어 구성

	3. **데이터 관리 조직**

		* 데이터 표준 원칙이나 데이터 표준의 준수 여부 등을 관리하는 사람들

			|   구분    |                      데이터 관리자 (DA)                      | 데이터베이스 관리자 (DBA) |
			| :-------: | :----------------------------------------------------------: | :-----------------------: |
			| 관리 대상 |                    데이터 모델, 각종 표준                    |       데이터베이스        |
			| 주요 업무 | 추가, 수정 등 사용자의 요구사항을 데이터에 반영<br />메타 데이터 정의 |     데이터베이스 관리     |
			| 품질 관리 |                   데이터 표준 관리 및 적용                   |   데이터의 정합성 관리    |

	4. **데이터 표준화 절차**

		|                 단계                 |                             수행                             |
		| :----------------------------------: | :----------------------------------------------------------: |
		| 1 단계 : 데이터 표준화 요구사항 수집 | 데이터 표준화와 관련된 자료 수집<br />시스템별 데이터 표준 수집<br />표준화 현황 진단 |
		|      2 단계 : 데이터 표준 정의       | 표준화 원칙 정의<br />표준 용어, 단어, 도메인, 코드 등 데이터 표준 정의 |
		|      3 단계 : 데이터 표준 확장       |                 데이터 표준 검토, 확정, 공표                 |
		|      4 단계 : 데이터 표준 관리       | 데이터 표준 적용, 변경, 준수 검사 등 데이터 표준 관리 절차 수립<br />데이터 표준 이행 |

	5. **데이터 표준화의 대상**

		1. **데이터 명칭** : 데이터를 유일하게 구별, 의미 전달, 업무적 보편성을 갖는 이름을 가져야 함
		2. **데이터 정의** : 제3자의 입장에서도 쉽게 이해할 수 있도록 데이터가 의미하는 범위와 자격 요건을 규정
		3. **데이터 형식** : 데이터 형식을 일관적으로 정의함으로써 데이터 입력 오류 및 통제 위험 최소화
		4. **데이터 규칙** : 데이터 값을 사전에 지정해 데이터의 정합성 및 완전성 향상

	6. **데이터 표준화의 기대효과**

		* 데이터의 의미나 위치를 파악하고 의사소통하기 쉽다

		* 데이터의 유지보수 및 운용에 용이하다

		* 입력 오류 등 방지효과

			