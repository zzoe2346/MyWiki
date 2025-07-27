---
tags: 
created: 2025-07-27
---
자바의 synchronized와 ReentrantLock 키워드를 말하는 것.

- synchronized
	- 이거 사용하면 더 간단히 스레드의 동시 접근 제어 가능
	- 코드 블록이 끝나면 자동으로 잠금을 풀어주기에 `unlock()`같은 메서드 호출 불필요
- ReentrantLock
	- 이거는 synchronized에 없는기능을 제공.
	- 바로, 잠금 획득 대기 시간을 지정 가능한 것.
	- 또한 자바 21에 추가도니 가상 스레드가 ReentrantLock만 지원하고 synchronized는 자바24 부터 지원함.