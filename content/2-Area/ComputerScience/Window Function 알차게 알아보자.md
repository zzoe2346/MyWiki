---
created: 2025-06-18
tags:
  - 윈도우함수
---
- ANSI : 2003 표준
- 지원 역사
	- Oracle - ver 8.1.6 in 2000
	- PostgreSQL - ver 8.4 in 2009
	- MySQL - ver 8 in 2018
	- MariaDB - ver 10.2 in 2016
	- SQLite - rel 3.25.0 in. 2018
- analytic funtion이라고도 불림(기원: Oracle)

## 동작 순서
- `ROW_NUMBER()` 같은 윈도우 함수는 `WHERE`에서 직접 사용 불가
	- SQL 쿼리의 논리적 순서 상 `WHERE` 이 윈도우 함수보다 먼저 처리되기 때문
	- 따라서, 서브쿼리 or CTE(Common Table Expression) 써야됨
## 최신 조회에서 ROW_NUMBER()
- ROW_NUMBER()는 기본적으로 데이터를 정렬한 후에 순번을 매기는 함수
- ROW_NUMBER()의 임무는 도출된 데이터에 **순번을 매기는 것**(추가 속성 붙이기)

### 장점
- ANSI 표준으로 위에 나온 DBMS에 서로 이식이 용이함👍
### 단점
- 가독성
- 성능
	- 단순 최신 조회에서는 `TOP`, `LIMIT` 같은게 더 빠름
		- 옵티마이저는 `LIMIT 5`이런 구문을 보고 정렬된 인덱스를 스캔하다가 5개 행 찾으면 즉시 작업을 멈추기 때문
		- 이 구문들은 데이터베이스 옵티마이저에게 "처음부터 N개만 찾으면 더 이상 작업을 할 필요가 없다"는 명확한 힌트를 줌
	- 반면, `ROW_NUMBER()`는 `WHRER` 전에 `OVER()` 기준에 따라 전체 대상 데이터의 순번을 계산(FULL SCAN)해야 함

> 실행 계획을 봐 보면 기존에 ROW_NUMBER() 쓰는 쿼리들은 create_at 같은 속성에 인덱스가 걸려있음에도 풀 스캔이 발생


**참고자료**
- https://en.wikipedia.org/wiki/Window_function_(SQL)#cite_note-:1-1
- https://en.wikipedia.org/wiki/SQL:2003
- https://dev.mysql.com/doc/refman/8.4/en/window-function-descriptions.html
- https://mariadb.com/docs/server/reference/sql-functions/secondary-functions/information-functions/rownum

**MariaDB 공식 문서 설명**
`ROWNUM()` returns the current number of accepted rows in the current context. It main purpose is to emulate the `ROWNUM`[pseudo column in Oracle](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/ROWNUM-Pseudocolumn.html#GUID-2E40EC12-3FCF-4A4F-B5F2-6BC669021726). For MariaDB native applications, we recommend the usage of [LIMIT](https://mariadb.com/docs/server/reference/sql-statements/data-manipulation/selecting-data/limit), as it is easier to use and gives more predictable results than the usage of `ROWNUM()`.

The main difference between using `LIMIT` and`ROWNUM()` to limit the rows in the result is that`LIMIT` works on the result set while `ROWNUM` works on the number of accepted rows (before any `ORDER` or`GROUP BY` clauses).