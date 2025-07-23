---
created: 2025-04-27
---

https://docs.oracle.com/javase/8/docs/api/java/lang/Thread.html
> Java 8 기준 
## 개념과 기술 배경
- 자바는 멀티 쓰레드 기반으로 동시성 프로그래밍을 지원하기 위한 방법들을 계속해서 발전
- Thread, Runnable은 자바 초기부터 멀티 쓰레드를 위해 제공되던 기술.

![[SCR-20250426-ttoo.png]]

## Java 8 Thread Class Docs 파악
- 스레드는 우선순위를 가짐
	- 높은 우선순위 가진 스레드가 낮은 우선순위 스레드보다 우선적으로 실행
- 각 스레드는 데몬(deamon)으로 표시(mark)될 수도 있고 아닐 수도 있음
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
## Java 8 기준 Thread Class Code 파악

클래스의 메서드 역할의 경우는 문서를 참고하면 된다. 여기서는 코드 부분에서 공부한 부분을 정리해보았다.

## 사용 방법

새로운 스레드 생성 및 실행방법은 2가지 있다.
1. 클래스를 `Thread` 클래스의 하위 클래스로 선언
2. `Runnable` 인터페이스를 구체화한 클래스 선언

구체적으로 알아보면


There are two ways to create a new thread of execution. One is to declare a class to be a subclass of `Thread`. This subclass should override the `run` method of class `Thread`. An instance of the subclass can then be allocated and started. For example, a thread that computes primes larger than a stated value could be written as follows