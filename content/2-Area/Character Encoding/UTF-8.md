---
created: 2025-07-12
tags:
  - backend/character/term
---
Unicode Transformation Format
- UTF-8은 웹 서비스 포함 여러 분야에 가장 많이 사용되는 인코딩 규칙(사실상 표준)
	- 1~4바이트 가변적 길이
	- 공간 효율성 높으며 넓은 문자 지원 가능
- ASCII와 완벽히 호환
- 영문은 1바이트로 처리해 효율 극대화
- 영문 제외한 나머지는 2~4바이트로 가변길이로 인코딩
	- U+0041은 0x41 이며 'A'
	- '가'(U+AC00)은 0xEA 0xEB 0x80 3바이트!
	- 가~힣
- 유니코드를 변환시키는 포맷에 대한 정의
- 4비트로 16진수 표현

![[Pasted image 20250710163651.png]]
- 한글에 해당하는 유니코드를 가져다가 UTF로 인코딩하는것임

![[Pasted image 20250710164226.png]]- 0xEB 여기서 ==E==는 1110 즉 3바이트로 해석해야할 UTF란것

![[Pasted image 20250710164753.png]]![[Pasted image 20250710164759.png]]

가를 utf-8로 인코딩하면 3바이트가 되는것. utf-8은 가변으로 인코딩해줌. 용량이 최적화되지 필요한만큼 메모리 차지하니깐. 이제 디코딩할때는 1110 .... 10.. .... 10.. .... 이거보고 해석해서 디코딩하는것