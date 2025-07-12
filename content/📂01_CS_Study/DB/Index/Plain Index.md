- 인덱스는 꼭 유니크할 필요 없음
```sql
CREATE TABLE t2 (a INT NOT NULL, b INT, INDEX (a,b));

INSERT INTO t2 values (1,1), (2,2), (2,2);

SELECT * FROM t2;
+---+------+
| a | b    |
+---+------+
| 1 |    1 |
| 2 |    2 |
| 2 |    2 |
+---+------+
```