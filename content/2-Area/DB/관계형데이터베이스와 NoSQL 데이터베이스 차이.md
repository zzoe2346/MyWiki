---
created: 2025-05-10
tags:
  - NoSQL
---
- DB-Engines Ranking 참고해보면...

|         | RDBMS                     | NoSQL DB                             |
| ------- | ------------------------- | ------------------------------------ |
| Schema  | 엄격하고 정의 필요                | 유연하고 동적으로 변경 가능                      |
| 쿼리 언어   | SQL                       | JSON, API, Cypher(Neo4j) 등           |
| 트랜잭션    | 지원                        | 지원                                   |
| 격리성(기본) | repeatable read(MySQL)    | local := read uncommited(mongoDB)    |
| 스케일링    | 수직 스케일링이 더 쉬움 - 서버 성능 향상  | 수평 스케일링이 더 쉬움 - 서버 대수 증가             |
| 예       | MySQL, Oracle, PostgreSQL | MongoDB, Redis, ElasticSearch, Neo4j |
