---
created: 2025-04-27
---
- 메인 스레드 종료 시 강제종료되는 스레드
- 어떤 스레드에서 실행되는 코드가 새로운 Thread 객체를 생성할 때
	- 새로운 스레드의 우선순위는 처음에 생성 스레드의 우선순위와 동일하게 설정
	- 생성 스레드가 데몬인 경우에 그 스레드는 데몬 스레드가 됨
- JVM 시작시, 대게 single non-daemon thread 가 있음
- JVM이 부팅되면 "메인 스레드" 하나를 만들어서 `main()` 메서드를 실행
	- 이 스레드는 일반적으로 지정된 클래스의 main이라는 메서드를 호출합니다.
- JVM은 스레드들을 계속 실행시킴. 다음의 현상이 발생할 때 까지
	- Runtime.exit() 호출
		- `Runtime`클래스의 `exit`메서드가 호출하고, 시큐리니 매니저가 이 익싯 오퍼레이션이 발동하는걸 허락했을 때
	- 모든 non-daemon 스레드 종료
		- `run()` 다 실행하고 리턴
		- `run()` 안에서 예외 발생
		- 데몬 스레드만 있으면 JVM은 죽음.
- `thread.setDeamon(true)` 로 두면 메인 스레드 죽으면 같이 죽음
- 데몬스레드== 워커스레드로 생각하며되는듯