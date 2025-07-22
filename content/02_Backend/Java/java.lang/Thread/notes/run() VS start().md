---
created: 2025-04-27
---
https://babgeuleus.tistory.com/entry/%EB%A9%80%ED%8B%B0%EC%8A%A4%EB%A0%88%EB%93%9C%EC%99%80-%EB%8F%99%EA%B8%B0%ED%99%94-%EB%AC%B8%EC%A0%9C-%EB%B0%B1%EA%B8%B0%EC%84%A0-%EC%9E%90%EB%B0%94%EB%9D%BC%EC%9D%B4%EB%B8%8C%EC%8A%A4%ED%84%B0%EB%94%94
만약 run()을 메인 함수에서 실행시킨다면

![[Pasted image 20250427160855.png]]
이런 스택 영역이 될 것이다. 메인 스레드에서 기껏 만든 스레드 코드를 그냥 실행 시키는 것. 이는 새로운 스레드를 생성하는게 아닌 싱글 스레드로 동작 시키는 것.