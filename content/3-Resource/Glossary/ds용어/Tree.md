---
created: 2025-07-16
tags:
  - cs/datastructure/term
---
- 노드들 집합
- 각 노드는 값과 다른 노드 가리키는 레퍼런스로 구성
	- **값 + 레퍼런스(포인터)**
- 특징
	- **루트 노드는 하나만 존재**
	- **사이클 없음**
	- 자녀 노드는 하나의 부모 노드만 존재
	- nonlinear 구조
	- 트리에 서브 트리 있는 재귀적 구조
	- 계층적 구조
- 용어
	- 간선(edge)
		- 노드와 노드 연결하는 선
		- 구현 관점에서 레퍼런스 의미
	- 루트 노드
		- 트릐의 최상단에 있는 노드
		- 시작점
	- 자녀 노드
		- 모든 노드는 0개 이상의 자녀 노드 가짐
	- 부모 노드
		- 자녀 노드 가지는 노드
	- 형제 노드
		- 같은 부모 가지는 노드
	- 조상 노드
		- 부모 노드 따라 루트 노드 까지 올라가며 만나는 모든 노드
	- 자손 노드
		- 자녀 노드를 따라 내려가며 만날수 있는 모든 노드
	- 내부 노드
		- 자녀 노드 가지는 노드
	- 외부 노드
		- 자녀 노드 없는 노드
		- 리프 노드
	- 노드의 높이
		- 노드에서 리프 노드 까지의 가장 긴 경로의 **간선 수**
	- 트리의 높이
		- 루트 노드의 높이
	- 노드의 깊이
		- 루트 노드에서 해당 노드까지 경로의 간선 수
		- 루트 노드의 깊이 = 0
	- 트리의 깊이
		- 트리에 있는 노드들의 깊이 중 가장 긴 깊이
		- 트리 높이 == 트리 깊이
	- 노드의 차수(degree)
		- 노드의 자녀 노드 수
	- 트리의 차수
		- 트리에 있는 노드들의 차수 중 가장 큰 차수
	- 두 노드 사이 거리
		- 두 노드의 최단 경로 간선 수
	- 노드의 레벨
		- 노드와 루트 노드 사이의 경로에서 간선의 수
		- 루트 노드의 레벨은 0 (혹은 1로 하는것도 있음)
	- width
		- 임의 레벨에서 노드의 수
	- 노드의 크기
		- 자신 포함한 자손 노드 수
	- 트리 크기
		- 트리의 모든 노드 수
	- 서브 트리
		- 각 노드의 자녀 노드들을 재귀적으로 서브트리를 구성한다.