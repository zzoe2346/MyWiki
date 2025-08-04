---
created: 2025-06-19
tags:
  - cs/db/term
---
- Exclusive Lock
- 이거 수정할꺼니까 건드리지 마셈
- 한 트잭이 데이터 수정하는 동안 다른 트잭이 수정 못하도록 막음
- X Lock걸린 레코드에는...
	- 다른 트잭이 X Lock획득 불가
	- 다른 트잭이 S Lock획득 불가
	- 다른 트잭이 해당 레코드 UPDATE, DELETE 불가
	- 단, 락 없는 일반 SELECT 쿼리는 허용됨(MVCC 특성!)
```sql
START TRANSACTION;
SELECT * FROM accounts WHERE id = 1 FOR UPDATE -- X-LOCK
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
COMMIT;
```