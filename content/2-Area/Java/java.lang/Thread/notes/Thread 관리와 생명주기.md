---
created: 2025-04-27
---
- 스레드 상태
	- New
	- Runnable
	- Running
	- Blocked
	- Waiting
	- Terminated
- start(), sleep() and join()으로 스레드 생명주기 관리
	- start() : 스레드 실행
	- sleep() : 일정 시간 동안 스레드 중지(pause)
	- [[join()]] : 메인 스레드(여기선 `main()`)가 호출한 다른 스레드 끝날때 까지 대기. 
![[Pasted image 20250427100137.png]]