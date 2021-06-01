### 프로그래머스 - DATETIME에서 DATE로 형 변환

### Level 2

---

**링크** : https://programmers.co.kr/learn/courses/30/lessons/59414

**풀이**

* **DATE_FORMAT**을 사용해야한다.

	1. **사용법**

		* ```sql
			DATE_FORMAT(날짜, 형식) : 날짜를 지정한 형식으로 출력
			```

	2. **구분기호**

		| 구분기호 |          역할           |
		| :------: | :---------------------: |
		|    %Y    |       4자리 년도        |
		|    %y    |       2자리 년도        |
		|    %M    |       긴 월(영문)       |
		|    %b    |      짧은 월(영문)      |
		|    %W    |      긴 요일 이름       |
		|    %a    |     짧은 요일 이름      |
		|    %i    |           분            |
		|    %T    |        hh:mm:SS         |
		|    %m    |   숫자 월 ( 두 자리)    |
		|    %c    |         숫자 월         |
		|    %d    |      일 ( 두 자리)      |
		|    %e    | 일 ( 한자리는 한자리로) |
		|    %l    |        시간 (12)        |
		|    %H    |        시간 (24)        |
		|    %r    |     hh:mm:ss AM,PM      |
		|    %S    |           초            |

**코드**

```sql
SELECT ANIMAL_ID, NAME, DATE_FORMAT(DATETIME, "%Y-%m-%d") as 날짜
from animal_ins
```

