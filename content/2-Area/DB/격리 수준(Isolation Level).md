---
created: 2025-07-12
tags:
  - cs/db/concept
---
- [[2-Area/DB/개념/Serializable]]
- [[Repeatable Read]]
- [[Read Commited]]
- [[Read Uncommited(읽기 허용, 커밋 안됨)]]

격리성이 높을수록 데이터 정합성(무결성)은 높아지지만, 성능은 저하

|                 | [[Dirty Read]] | [[Non-repeatable Read]] | [[Phantom Read]] |
| --------------- | -------------- | ----------------------- | ---------------- |
| Read Uncommited | O              | O                       | O                |
| Read Commited   | X              | O                       | O                |
| Repeatable Read | X              | X                       | O                |
| Serializable    | X              | X                       | X                |
