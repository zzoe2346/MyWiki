---
created: 2025-06-17
tags:
  - cs/hw/term
---
- cpu 동작 일련의 단계
- 단계
	- 1 명령 인출(fetch)
		- 제어 장치가 주 메모리 또는 캐시에서 명령 읽어와 CPU 에 복사
		- 이 과정에서 제어장치는 다양한 카운터 이용해 어떤 명령을 어디서 읽어와야 하는지 결정
	- 2 명령 해석(decode)
		- fetch 에서 읽어온 명령어 처리할 수 있도록 해석
		- 명령의 연산 코드에 따라 정해진 처리 장치로 명령이 전달
	- 3 실행(execute)
		- 명령이 ALU로 전달되어 실행됨
	- 4 결과 저장
		- 명령 수행 끝나면 결과를 RAM 에 저장 후, 다음 명령 실행할 준비한다
		- 그리고 1 단계인 fetch로 돌아감.
	- ==이 4과정을 명령이 없을 때까지 반복==

