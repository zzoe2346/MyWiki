---
created: 2025-07-16
tags:
  - cs/datastructure/term
---
- 배열과 [[Hash Function]]해시 함수를 사용하여 map을 구현한 자료구조
	- 변환된 키값을 나머지 연산으로 제한시킬수있다.
- 일반적으로 테이블의 **크기에 상관없이 key 통해 상수 시간에 빠르게 데이터 접근 가능**
- 테이블에 넣어지는 값은 총 3개!!!
	- key, (삽입 당시 계산된)hash, value
- (일반적으로) 상수 시간으로 데이터 접근하기에 빠름
- [[Hash Collision]]의 가능성
- Java 의 경우 HashMap 구현체를 Hash Table을 씀
	- CPython은 충돌 해결 방법이 Open Addressing인 반면 자바는 Separate Chaining
	- 75% 차면 2배씩 리사이징함.
	- 축소는 안한다.

## Hash Table Resizing
데이터가 많이 차면 크기를 늘려줘야 한다.[[Hash Collision]]의 Open Addressing 참고