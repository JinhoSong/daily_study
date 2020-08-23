### 정보처리기사

---

1. **데이터 베이스 구축**
	1. **SQL 응용**
		1. [SQL의 개념](#SQL의 개념)
		2. [DDL](DDL)
		3. [DCL](DCL)
		4. [DML](#DML)
		5. [DML-SELECT-1](#[DML-SELECT-1])
		6. [DML-SELECT-2](#[DML-SELECT-2)
		7. [DML-JOIN](#DML-JOIN)

---

1. ### SQL의 개념

	1. **SQL의 개요**
		* 국제 표준 데이터베이스 언어이며 많은 관계형 RDB를 지원하는 언어로 채택
	2. **SQL의 분류**
		* **DDL**
			1. 데이터 정의 언어
			2. 스키마, 도메인, 테이블, 뷰 , 인덱스를 정의 변경 삭제
			3. create : 스키마, 도메인 테이블, 뷰, 인덱스 정의
			4. alter : update
			5. drop : delete
		* **DML**
			1. 데이터 조작 언어
			2. select : 조건에 맞는 검색
			3. insert : 삽입
			4. delete : 삭제
			5. update : 변경
		* **DCL**
			1. **데이터의 보안, 무결성, 회복, 병행 수행 제어 등을 정의하는데 사용하는 언어**
			2. commit : 명령에 의해 수행된 결과를 실제 물리 디스크에 저장하고 조작 작업이 정상적 완료했음을 알려준다.
			3. rollback : 데이터베이스 조작 작업이 비정상적으로 종료되었을 때 원래의 상태로 복구
			4. grant : 데이터베이스 사용자에게 사용 권한을 부여

2. ### DDL

	1. **DDL의 개념**

		* 데이터 정의어는 DB 구조, 데이터 형식, 접근 방식 등 DB를 구축하거나 수정할 목적으로 사용하는 언어
		* DDL의 종류에는 create, alter, drop이 있음

	2. **CREATE SCHEMA**

		* create schema 스키마명 authorization 사용자_id;

	3. **CREATE DOMAIN**

		* 임의의 속성에서 취할 수 있는 값의 범위가 SQL에서 지원하는 전체 데이터 타입의 값이 아니고 일부분일 때, 사용자는 그 값의 범위를 도메인으로 정의할 수 있다.
		* 정의된 도메인명은 일반적인 데이터 타입처럼 사용한다.

	4. **CREATE TABLE**

		* 테이블 정의

		1. PRIMARY KEY
		2. **UNIQUE** : **대체키로 사용할 속성 또는 속성 집합, 지정한 속성은 중복된 값을 가질 수 없다**
		3. **FOREIGN KEY ~ PERFERENCES**
		4. CONSTRAINT : 제약 조건의 이름을 지정한다. 이름을 지정할 필요가 없으면 CHECK절만 사용하여 속성 값에 대한 제약 조건을 명시한다.
		5. CHECK : 속성 값에 대한 제약 조건을 정의한다.

	5. **CREATE VIEW**

		* CREATE VIEW는 뷰를 정의 명령문
		* create view 뷰명 [(속성명[, 속성명,....])] as select;
		* **select문을 서브 쿼리로 사용하여 결과로서 view를 생성한다.**
		* **서브 쿼리**에서는 UNION이나 order by 절을 사용할 수 없다.

	6. **CREATE INDEX**

		* create [UNIQUE] index 인덱스명 ON 테이블명 ( 속성명 [ASC | DESC] [ 속성명 [ASC | DESC]]) [CLUSTER];

		* **UNIQUE**
			1. 사용된 경우 : **중복 값이 없는 속성으로 인덱스를 생성한다**
			2. 생략된 경우 : **중복 값을 허용하는 속성으로 인덱스를 생성한다**
		* **정렬 여부 지정**
			1. ASC : 오름
			2. DESC : 내림
			3. 생략된 경우 : 오름
		* **CLUSTER** : 사용하면 인덱스가 클러스터 인덱스로 설정됨

	7. **ALTER TABLE**

		* 테이블에 대한 정의를 변경하는 명령문
		* alter table 테이블명 add 속성명 데이터_타입 [default 기본값]
		* alter table 테이블명 alter 속성명 [set default 기본값];
		* alter table 테이블명 drop column 속성명 [cascade]
		* ADD : 새로운 열을 추가

	8. **DROP**

		* drop table 테이블명 [CASCADE | RESTRICTED];
		* **CASCADE** : 제거할 요소를 참조하는 다른 모든 개체를 함께 제거한다.
		* **참조 무결성 제약 조건을 설정하기 위해 사용된다**
		* **RESTRICTED** : 다른 개체가 제거할 요소를 참조중일 때는 제거를 취소한다.

3. ### DCL

	1. **DCL의 개념**
		* **데이터의 보안, 무결성, 회복, 병행 제어 등을 정의하는 데 사용하는 언어
	2. **GRANT / REVOKE**
		* GRANT : 권한을 부여하거나 취소하기 위함
		* REVOKE : 권한 취소를 위한 명령어
		* grant 권한_리스트 on 개체 to 사용자 [with grant option];
		* revoke [grant option for] 권한_리스트 on 개체 from 사용자 [cascade];
		* **grant option for** : 다른 사용자에게 권한을 부여할 수 있는 권한을 취소함
		* **CASCADE** : 권한 취소 시 권한을 부여 받았던 사용자가 다른 사용자에게 부여한 권한도 연쇄적으로 취소함
	3. **COMMIT**
		* 트랜잭션이 성공적으로 끝난 후 변경된 내용을 데이터베이스에 반영
		* DML 성공시 자동으로 COMMIT
		* 실패시 ROLLBACK된다.
	4. **ROLLBACK**
		* COMMIT 되지 않은 변경된 내용을 취소하고 데이터베이스를 이전 상태로 되돌리는 명령
	5. **SAVEPOINT**
		* 트랜잭션 내에 ROLLBACK할 위치인 저장점을 저장

4. ### DML

	1. **DML의 개념**
		* 사용자가 응용 프로그램이나 질의어를 통해 저장된 데이터를 실질적으로 관리하는데 사용되는 언어이다.
	2. **INSERT INTO**
		* insert into 테이블명 value (데이터1,2,3,4)
	3. **DELETE FROM**
		* delete from 테이블명 where 조건명;
	4. **UPDATE SET**
		* update 테이블명 set 속성명 = 데이터 where 조건;

5. ### DML-SELECT-1

	1. **일반 형식**

		```sql
		select distinct school
		from student
		where name='홍길동'
		group by age
		having age>=17
		order by asc
		```

	2. **기본 검색**

		```sql
		select * from 사원;
		```

	3. **조건 지정 검색**

		* where 절에 조건을 지정하여 조건에 만족하는 튜플만 검색한다.

		```sql
		select 이름 from 사원 where 이름 like "김%"
		select * from 사원 where 생일 between #01/01/69# and #12/31/73#;
		```

	4. **정렬 검색**

		```sql
		select * from 사원 order by 주소 desc
		```

	5. **하위 질의**

		```sql
		select 이름,주소 from 사원 where 이름 = (select 이름 from 학생부 where age=27)
		```

		* **27살 이상의 학생이면서 사원인 사람의 이름과 주소**

	6. **복수 테이블 검색**

		* 여러 테이블을 대상으로 검색을 수행한다.

		```sql
		select 사원.이름, 사원.부서. 여가활동.취미
		from 사원, 여가활동
		where 여가활동.경력 > 10 and 사원.이름= 여가활동.이름
		```

6. ### DML-SELECT-2

	1. **일반 형식**

		1. group by
		2. having
		3. **window 함수**
			* GROUP BY 절을 이용하지 않고 속성의 값을 집계할 함수를 기술한다.
			* **Partition by : window함수가 적용될 범위로 사용할 속성을 지정한다.**
			* **order by : partiton 안에 정렬 기준으로 사용할 속성을 지정한다.**
			* **row_number() : 원도우별로 각 레코드에 대한 일련 번호를 반환합니다.**
			* **rank() : 윈도우별로 순위를 반환하며, 공동 순위를 반영합니다.**
			* **DENSE_RANK() : 윈도우별로 순위를 반환하며, 공동 순위를 무시하고 순위를 부여합니다.**
		4. 그룹함수
			* group by 절에 지정된 그룹별로 속성의 값을 집계할 함수를 기술한다.

	2. **WINDOW 함수 이용 검색**

		* 지정한 속성 범위로 다시 속성의 값을 집계하는 형식

		```SQL
		SELECT 상여내역,상여금
		ROW_NUMBER() OVER (PARTITION BY 상여금 ORDER BY 상여금 DESC) AS NO
		FROM 상여금;
		```

		* 상여내역을 기준으로 상여금 순서를 보여주며 중복된 값은 위부터 1,2,3, 순서로 내려온다.

		```SQL
		SELECT 상여내역,상여금
		RANK() OVER (PARTITION BY 상여내역 ORDER BY 상여금 DESC) AS 상여금 순위
		FROM 상여금;
		```

		* 상여내역을 기준으로 상여금의 순위를 정해 보여준다.
		* **중복된 값은 중복 순위를 보여준다.**

	3. **그룹 지정 검색**

		```sql
		select 부서,상여내역, sum(상여금) as 상여금합계
		from 상여금
		group by ROLLUP(부서,상여내역);
		```

		* 부서별 상여금의 합계와
		* 상여금의 총 합계가 나온다.

	4. **집합 연산자를 이용한 통합 질의**

		1. UNION : 두 select문의 결과를 통합하고 중복된 행은 한 번만 출력
		2. UNION ALL : 두 select문의 결과를 통합하고 중복된 행도 그대로 출력
		3. INTERSECT : 두 select 문의 결과 중 공통된 행만 출력
		4. EXCEPT : 첫번째 select문의 결과에서 두 번째 select문의 결과를 제외한 행을 출력

7. ### DML-JOIN

	1. **JOIN의 개념**

		* 2개의 TABLE에 대해 연관된 튜플들을 결합하여 하나의 새로운 릴레이션을 반환한다.
		* inner , outer 로 구분된다.

	2. **INNER JOIN**

		* 조건 없는 equi join을 실행하면 **cross join**이라고 한다. 

		1. **EQUI JOIN**

			* join대상 테이블에서 공통 속성을 기준으로 '=' 비교에 의해 같은 값을 가지는 행을 연결하여 결과 생성
			* **NATURAL JOIN**하면 중복값을 제외해줌

		2. **NON-EQUI JOIN**

			* join 조건에 '=' 조건이 아닌 나머지 비교 연산자, <,>, <>, <= 등을 이용한 join이다

		3. **OUTER JOIN**

			* 좌우로 나뉨
			* 기준에는 존재하지만 반대쪽에는 항이 없는 경우 NULL로 처리한다.
			* FULL OUTER JOIN은 좌우 동시에 적용한 것이다.

		4. **SELF JOIN**

			* 같은 테이블 2개의 속성을 연결하는 것

				