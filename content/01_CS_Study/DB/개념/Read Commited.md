---
created: 2025-07-12
tags:
  - cs/db/concept
---
- 커밋 완료된 데이터만 조회 가능
	- Dirty Read 방지 가능
- 가장 일반적인 격리성 레벨
- PostgreSQL, SQL Server, Oracle, Mssql 의 기본 단계
- !!! 같은 트랜잭션에서 같은 데이터를 조회해도 커밋한 다른 트랜잭션에 의해서 다른 값이 나올 수 있는 문제 존재함!!!
	- ==[[Non-repeatable Read]] 발생 가능!==
- 가장 많이 사용되는 격리 수준
	- 팬텀 리드, 반복 가능하지 않은 조회 가능성 존재