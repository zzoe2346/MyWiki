---
created: 2025-05-06
---
#java #completeableFuture #async 

https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html

Java8 부터 제공. 복잡한 비동기 프로그래밍을 쉽게 다루기 위해 설계된 클래스.

Future, Callback 활용.

기존 Future에서 여러 Future의 결과 값을 조합, 조작하거나 예외를 효과적으로 핸들링할 수 없던 문제인 ==get() 호출 시 작업이 완료될 때 까지 블로킹 되는 문제를 해결.==

퓨처 클래스의 한계를 극복하기 위함. 퓨처는 비동기 처리 결과를 참조할 때 쓰임.

비동기 프로그래밍 이란? **논블록 코드**를 작성하는 것. **다른 스레드**에서 실행에 의해서 메인 스레드가 아닌. 메인 스레드는 계속 실행할 수 있음 다른 일들을 이어서 **병렬적**으로.
## 주요 특징 및 기능

- **비동기 작업 실행**
    - `runAsync()`: 반환값이 없는 비동기 작업 실행
    - `supplyAsync()`: 반환값이 있는 비동기 작업 실행[^1][^7]
- **작업 콜백 및 체이닝**
    - 작업이 완료된 후 실행할 콜백을 등록할 수 있고, 여러 비동기 작업을 체인 형태로 연결할 수 있습니다[^1][^6][^7].
- **작업 조합**
    - 여러 비동기 작업의 결과를 조합하거나, 결과를 다른 작업의 입력으로 사용할 수 있습니다. 대표적으로 `thenCompose()`, `thenCombine()` 등이 있습니다[^5][^4].
- **예외 처리**
    - 비동기 작업 도중 발생하는 예외를 효과적으로 처리할 수 있는 메서드(`exceptionally()`, `handle()`, 등)를 제공합니다[^1][^5][^6].
- **외부에서 완료 가능**
    - Future와 달리 외부에서 명시적으로 완료시킬 수 있습니다. 예를 들어, 특정 조건에서 직접 결과를 설정하거나 예외로 완료할 수 있습니다[^1][^4].
## 기존 Future와의 차이점

| Future | CompletableFuture |
| :-- | :-- |
| 단일 연산 결과만 표현 | 여러 비동기 작업의 조합 가능 |
| 외부에서 완료 불가 | 외부에서 명시적 완료 가능 |
| 콜백, 체이닝 미지원 | 콜백, 체이닝, 예외처리 등 지원 |
| 예외 처리 제한적 | 다양한 예외 처리 메서드 제공 |

https://www.perplexity.ai/search/completablefuture-6yQ_7ePeQSSBlrCmTHjx7Q

https://11st-tech.github.io/2024/01/04/completablefuture/

https://mangkyu.tistory.com/263