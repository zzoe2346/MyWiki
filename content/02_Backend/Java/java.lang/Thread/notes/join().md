---
created: 2025-04-27
---
이해하기 위한 예제. https://defacto-standard.tistory.com/1191 에서 퍼왔다. `join()`에 대한 정성스러운 분석글.

```java
public class JoinExample {

    public static void main(String[] args) {
        System.out.println("메인 스레드 시작");

        // 작업을 수행할 스레드 생성
        Thread workerThread = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("작업 스레드 시작");
                try {
                    // 작업 스레드가 2초 동안 무언가를 한다고 가정 (sleep)
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    System.out.println("작업 스레드 인터럽트됨");
                    // 인터럽트 상태 재설정
                    Thread.currentThread().interrupt();
                }
                System.out.println("작업 스레드 종료");
            }
        });

        // 작업 스레드 시작
        workerThread.start();

        try {
            // 메인 스레드는 workerThread가 끝날 때까지 기다립니다.
            System.out.println("메인 스레드는 작업 스레드 종료를 기다립니다.");
            workerThread.join(); // workerThread가 끝날 때까지 메인 스레드 블록

            // 만약 특정 시간만 기다리고 싶다면:
            // workerThread.join(1000); // 최대 1초만 기다림

        } catch (InterruptedException e) {
            System.out.println("메인 스레드 인터럽트됨 (작업 스레드 기다리는 중)");
            Thread.currentThread().interrupt();
        }

        System.out.println("메인 스레드 종료 (작업 스레드 완료 후)");
    }
}
```

```
메인 스레드 시작
작업 스레드 시작
메인 스레드는 작업 스레드 종료를 기다립니다.
(약 2초 후)
작업 스레드 종료
메인 스레드 종료 (작업 스레드 완료 후)

```