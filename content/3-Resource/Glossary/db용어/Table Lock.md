---
created: 2025-06-19
tags:
  - cs/db/term
---
- InnoDB는 기본적으로 '행 단위 락'을 사용하지만, 테이블 단위 락도 존재
- 테이블 전체를 잠그는 방식
- 동시성 낮아지는 대신 데이터 정합성 강하게 보장
- 테이블 락이 걸려있는 동안, 다른 트랜잭션은 해당 테이블에 대한
	- SELECT
	- INSERT
	- UPDATE
	- DELETE
	- 불가

```sql
LOCK TABLES table_name READ -- 읽기 전용 락(읽기는 가능)
LOCK TABLES table_name WRITE; -- 쓰기 락 (읽지도 마라)
UNLOCK TABLES; -- 락 해제
```
## 사용예시
1. 테이블 백업시(READ LOCK)
	1. 백업동안 테이블 변경 노노
	2. 따라서, 테이블 전체에 READ LOCK걸고 백업 수행
2. 데이터 마이그레이션 시(WRITE LOCK)
	1. 특정 테이블 데이터 마이그레이션시, 데이터 읽거나 변경되면 안됨
	2. 따라서, 테이블 전체에 WRITE LOCK걸고 마이크레이션을 수행