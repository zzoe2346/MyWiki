---
created: 2025-06-24
---
- 갑이 코드 진행한다.
- 동기적으로 안해도되는 메서드를 을 스레드에 던짐
- 근데 을은 이거 바로 안주고 갑 기다리게함.

Sync Blocking 과 큰 차이 없다

보통 Async-blocking은 개발자가 비동기 논블록킹으로 처리 하려다가 실수하는 경우에 발생하거나, 자기도 모르게 블로킹 작업을 실행하는 의도치 않은 경우에 사용된다. 그래서 이 방식을 안티 패턴(anti-pattern)이라고 치부하기도 한다.

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class AsyncBlockingIOExample {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newSingleThreadExecutor();

        // 비동기적으로 작업 제출
        Future<String> futureResult = executor.submit(() -> {
            // 오래 걸리는 I/O 작업 시뮬레이션
            Thread.sleep(3000);
            return "작업 완료!";
        });

        System.out.println("작업 요청 완료, 다른 작업 수행...");

        // ... 다른 작업 수행 ...

        System.out.println("결과를 기다립니다...");
        // 결과를 기다리는 동안 스레드는 블로킹됨
        String result = futureResult.get();
        System.out.println("결과: " + result);

        executor.shutdown();
    }
}
```

---
그로킹 동시성

- 리액터 패턴
- ? 놀랍게도 비동기 블로킹 모델이지만, 입출력 연산은 논블로킹으로 동작
- 그러나, 바쁜 대기 방식이 아닌 특별한 블로킹 시스템 콜인 `select`사용해서 입출력 연산 상태를 통지 받음.
- 그러므로 블로킹이 일어나는 부분은 입출력 연산 호출이 아니라 상태 통지 부분
- 그러므로 통지 시스템의 신뢰성과 성능이 확보만됨다면 매우 뛰어난 입출력 선은 보일수있는 모델!