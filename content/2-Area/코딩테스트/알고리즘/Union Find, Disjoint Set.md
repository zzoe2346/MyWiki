---
tags: 
created: 2025-08-28
---
https://youtu.be/rE-OUyZJgOk?si=5hXxdwgh2rvHEZBa

## 1. 정의와 성질
- Union - 두 그룹을 합치는 연산
- Find - 원소가 속해 있는 그룹을 알아내는 연산
최적화 전에 배열을 탐색하는 방식으로 union(u, v) 명령어가 들어오면 u의 그룹을 확인하고, 전체 원소를 살펴보면서 v와 같은 그룹인 원소들의 그룹을 u의 그룹으로 바꿔야된다. 그래서 시간복잡도를 보면 union은 O(N). 

**하지만** 최적화 과정을 거치면 거의 상수시간에 해결이 가능해진다. 이걸 이제 알아본다.
## 2. Union 연산
실제 구현에서는 각 원소를 정점으로 생각하고 그룹은 트리로 표현한 다음 union 연산이 발생때 마다 한 트리의 루트다 다른 트리의 자식으로 들어가서 두개의 트리가 합쳐진다.
### 그림으로 이해
부모 정점이 -1 인것은 부모 정점이 없다는 뜻
![[SCR-20250828-lelh.png]]
초기 상태
![[SCR-20250828-lepi.png]]
5번 정점을 1번 정점의 자식으로 만든다.
![[SCR-20250828-lexr.png]]
이번엔 3번 정점을 2번 정점의 자식으로 만든다.
![[SCR-20250828-lfee.png]]
어? 여기선 3번 정점을 1번 정점의 자식으로 만들면 안된다. 만약 그렇게되면 2번 정점이 고아가 되기 때문.**이걸 해결하기위해 3번 정점의 루트인 정점 2번을 1번 정점의 자식으로 만들어야한다.**

![[SCR-20250828-lfzq.png]]
이번에는 유니온 2, 6이다. 즉, 6번 정점이 속한 그룹의 루트 정점을 2번 정점이 속한 그룹의 자식으로 만들면 된다.

## 3. Find 연산
속한 트리의 루트를 알아내면 소속을 알 수 있다.
Find3 3->2->1->-1 -1이면 자기가 루트정점이라는 의미!
## 4. 구현
### Union Find 구현
```java
int[] p = new int[]{11, -1} ;
//최악 O(N)
int find(int x){
	if(p[x] < 0){
		return x;
	}
	return find(p[x]);
}

boolean uni(int u, int v){
	//v가 속한 루트정점을 u가 속한 그룹의 루트정점 자식화 한다.
	int rootOfU = find(u);
	int rootOfV = find(v);

	if(rootOfU == rootOfV){
		return false;
	}
	p[rootOfV] = rootOfU;
	return true;
}
```

## 5. 최적화 기법
![[SCR-20250828-qowj.png]]
### 5.1 최적화 1 - Union by rank
```java
boolean uni(int u, int v){
	//v가 속한 루트정점을 u가 속한 그룹의 루트정점 자식화 한다.
	int rootOfU = find(u);
	int rootOfV = find(v);

	if(rootOfU == rootOfV){
		return false;
	}
	if(p[rootOfV] < p[rootOfU]){
		swap(rootOfV, rootOfU);
	} // V의 랭크가 더 큰 경우
	//위 if로 인해 u의 랭크 >= v의 랭크 다.
	if(p[rootOfV] == p[rootOfU]){
		p[rootOfU]--;//랭크같으면 랭크를 1 증가시킴
	}
	p[rootOfV] = rootOfU;
	return true;
}
```
### 5.2 최적화 2 - 경로 압축
```java
int[] p = new int[]{11, -1} ;
//최악 O(N)이긴한데 Amorteized O(logN) 로 개선
int find(int x){
	if(p[x] < 0){
		return x;
	}
	//return find(p[x]);
	return p[x] = find(p[x]);//이걸로 바꾸면됨! 심플
}
```
## 6. 연습 문제
