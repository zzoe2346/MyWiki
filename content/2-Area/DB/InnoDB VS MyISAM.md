---
created: 2025-05-12
tags:
  - cs/db/concept
---
- 스토리지 엔진
	- 데이터 저장, 검색, 트랜잭션 관리, 동시성 제어, 캐싱 등 핵심 담당 구성 요소


|            | InnoDB | MyISAM   |
| ---------- | ------ | -------- |
| 트랜잭션 지원 유무 | 지원     | 미지원      |
| 락 및 동시성    | 행 레벨 락 | 테이블 레벨 락 |
| 외래 키 제약    | 지원     | 미지원      |
