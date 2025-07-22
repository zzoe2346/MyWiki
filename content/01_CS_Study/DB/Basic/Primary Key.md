---
created: 2025-05-12
---
https://mariadb.com/kb/en/getting-started-with-indexes/
- Unique
- Never `NULL`
- InnoDB 테이블에서는 모든 인덱스들은 PK를 접미사로서 포함한다.
	- 그러므로, 가능한 작게 PK를 하는게 중요
	- 만약 PK 없고, UNIQUE INDEX 마저 없다면 InnoDB 는 6바이트의 유저에게 안보이는 clusterd index를 만듬

!!! (MariaDB에서)Primary Key 가 없는 테이블 만들 수 있다