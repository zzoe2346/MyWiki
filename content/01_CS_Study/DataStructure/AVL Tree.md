---
date: 2025-07-17
tags:
  - Tree
---
## AVL Tree란
- [[Binary Search Tree(BST)]]의 한 종류
- 스스로 Balance를 잡는 트리
- Balance Factor 통해 균형 유지
- 트리의 모든 노드들은 Balance Factor 가 -1, 0, 1 인 특징
## AVL Tree의 균형 잡기
https://youtu.be/syGPNOhsnI4?si=Ib8JcAcL_z8ZCru2 균형 잡는과정 잘 설명해주는 영상

- 트리에 삽입 혹은 삭제 후 BF = {1,0,-1} 아닌게 생기면 균형 맞추는 작업 수행
- 삽입/삭제의 경우 조상노드들의 BF를 조사해서 균형 작업 수행.
```
    10
   /  \
  5    15
 / \
3   7
```
위 트리에서 노드 2를 삽입한다면
- 노드 2 → 노드 3 → 노드 5 → 노드 10 순으로 밸런스 팩터 확인
- 노드 15는 확인하지 않음 (영향받지 않음)
이런 방식으로 AVL 트리는 삽입 후에도 O(log n) 시간에 균형을 유지.

## 시간복잡도 및 장단점

|        | best  | avg      | worst    |
| ------ | ----- | -------- | -------- |
| insert | 세타(1) | O(log n) | O(log n) |
| delete | 세타(1) | O(log n) | O(log n) |
| search | 세타(1) | O(log n) | O(log n) |
\* n은 트리의 수
- 최악의 경우에도 어느 연산이든 O(log n) 보장하는 장점.
- 엄격히 균형 유지하기에 삽입/삭제 시 트리 균형 확인하고 균형이 만약 깨지면 트리 구조 재조정하에 이 때 시간이 소요되는 단점.
## Balance Factor
임의 노드 x에 대해서
`BF(x) = h(lSubTree(x)) - h(rSubTree(x))`

