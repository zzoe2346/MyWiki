---
created: 2025-06-24
tags:
  - cs/os/term
---
- 입출력 중심의 부하가 큰 애플리케이션은 이벤트 기반 동시성이 더 효율적인 경우 많음(왜?)
- 전통적 스레드 기반 동시성 단점
	- 스레드 생성 및 관리
	- 컨텍스트 스위칭
	- 공유 메모리 관리, 락 관리
- 리액터 패턴에서는 위 없이도 동시성 구현 가능하게함
	- 동시성에 이벤트를 도입해 실행스레드를 하나로 줄였고, 자원 소모 크게 줄임