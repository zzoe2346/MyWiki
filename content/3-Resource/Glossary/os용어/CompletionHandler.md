---
created: 2025-07-01
tags:
  - cs/os/term
---
- CompletionHandler는 Callback 인터페이스로 java.nio.channels 에 포함
- 비동기 읽기/쓰기 작업이 완료(성공 및 실패)되면 정해진 메서드 호출되는 구조
- 실제 입출력 수행하는것은 JVM이 제공하는 NIO.2 비동기 채널 구현체 내부