---
created: 2025-06-24
tags:
  - cs/os/term
---
갑은 쭉 진행함

을 스레드에 메서드 처리 맡김

갑으로 바로 응답옴... 그런데 갑은 을에 맡긴 메서드의 응답이 필요함. 

갑은 계속 을에 확인을함..

결국 동기적으로 실행된다.

-> 로딩화면, 크롬 다운로드 밑에꺼 막 바차아는거

---
그로킹 동시성 왈

- 애플리케이션이 논블로킹 모드로 입출력 장치에 접근하는 입출력 모델
- 운영체제는 입출력 호출에 즉각 결과를 반환하지만, 대부분 결과는 장치가 준비 상태가 아니라는 내용이며, 나중에 다시 호출해야됨
- 이 경우 애플리케이션 코드는 바쁜 대기 방식으로 구현되며 효율이 많이 떨어짐
- 입출력 연산 끝나고 사용자 공간으로 데이터가 전송된 후 애플리케이션이 데이터를 사용 가능
