---
created: 2025-07-14
tags:
  - cs/datastructure/term
---
- 힙은 주로 [[Binary Tree]](이진트리) 기반으로 구현
- Max Heap 
	- 부모 노드 키가 자식 노드들의 키보다 크거나 같은 트리
- Min Heap
	- 부모 노드 키가 자식 노드들의 키보다 작거나 같은 트리
- 힙의 키를 우선순위로 사용한다면 ==힙은 [[Priority Queue]]의 구현체가 된다.==
	- Priority Queue == ADT
	- Heap == DataStructure
- insert 경우
	- 무조건 맨 끝에 넣는다. 이후 부모와 비교. 이후 과정과 다른 동작들은 아래 영상들 참고
- delete써서 heap sort 응용 가능.
	- delete가 아웃풋
- https://visualgo.net/en/heap
- https://www.youtube.com/watch?v=0wPlzMU-k00
- https://www.youtube.com/watch?v=P-FTb1faxlo&list=PLcXyemr8ZeoR82N8uZuG9xVrFIfdnLd72&index=2