---
created: 2025-07-15
tags:
  - cs/datastructure/term
---
- 키는 다른데 ==Hash==는 같을 때
- 키도 Hash도 다른데 `hash % map_capa`결과가 같을 때
- 키값으로 해시를 도출. 이 해시로 위치 찾고 그리로 가니 이미 있음. 그것의 해시전의 키값을보니 나랑 다름. 즉 충돌
## 해결 방식
### Separate Chaining
충돌나면 그 충돌위의 노드와 Link해준다
### Open Addressing(linear probing)
- separate chanining 과 다르게 비어있는 버킷을 이용함.
- 충돌나면 그 다음 빈 버킷에 넣는다.
- 삭제 후 빈 버킷에 더미데이터를 넣음. 다음 버킷에 데이터가 있을 수 도 있으니...
**오픈 어드레싱 방식에서 해시 테이블 리사이징**
해시키값을 모듈러 연산하여 옮긴다.
![[SCR-20250715-qnxw.png]]