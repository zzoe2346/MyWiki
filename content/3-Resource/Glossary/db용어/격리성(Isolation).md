---
created: 2025-06-15
tags:
  - cs/db/term
---
> 딩코
- 하나의 트랜잭션 완료될때까지, 다른 트잭이 특정 트랜잭션의 결과를 참조할 수 없어야함
	- 그러나그렇게되면 대기 시간이 엄청 오래걸릴 것
	- 따라서 적당히 조절해야되는데 이를 [[격리 수준(Isolation Level)]]이라함
- 격리성, 독립성
- 트랜잭션 수행시 서로 끼어 들지 못하는 것
- 병렬 트랜잭션은 서로 격리되어 마치 순차적으로 실행되는 것처럼 작동 해야함
- 여러 사용자가 같은 데이터에 접근할 수 있어야함
- 그런데! 순차적으로하면 성능이 나빠진다
	- 그래서! 여러 개의 격리 수준으로 나뉘어 제공됨

## 격리성과 동시성 관계
- 격리성이 높을수록 동시성은 낮아짐