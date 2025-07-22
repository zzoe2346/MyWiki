---
created: 2025-07-18
---
- 모든 노드의 좌측 서브 트리는 해당 노드 값보다 작은 값만 가지고 우측은 그 반대.

## 순회
- inorder trversal(중위 순회)
	- L D R
- preorder traversal(전위 순회)
	- D L R
- postorder traversal(후위 순회)
	- L R D
## 시간복잡도

|        | best  | avg      | worst |
| ------ | ----- | -------- | ----- |
| insert | 세타(1) | O(log n) | 세타(n) |
| delete | 세타(1) | O(log n) | 세타(n) |
| search | 세타(1) | O(log n) | 세타(n) |
## 장단점
- 장점
	- 삽입, 삭제가 유연
	- 값 크기 따라 좌우 서브 트리 나워져서 삽입,삭제, 검색이 (보통)빠름
	- 값의 순서대로 순회 가능
- 단점
	- 최악일때는 선형 속도
	- 구조적으로 한쪽으로 편향시 삽입, 삭제, 검색 등 여러 동작들의 수행 시간이 악화
	- 이를 개선하기 위해
		- [[AVL Tree]]
		- [[Red Black Tree]]