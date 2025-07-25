---
created: 2025-06-30
tags:
  - cs/os/term
---
- Decorating stream or filter stream
	- input/output Stream, Reader/Writer 와 조합
- 기본 스트림과 조합새 새로운 기능 추가
	- IO buffer 적용해 성능향상
	- char, byte 외 int, double 형식 입출력 지원
	- 객체 단위 직렬화
	- 바이트 스트림을 문자열로 변환(인코딩 변경 기능)
	- 형식문자 지원
- 여러 보조 스트림 조합해 확장 가능

**주요 보조 스트림**
- BufferedInput/OutputStream
	- Buffered IO 적용해 성능향상
- DataInput/OutputStream
	-  int, double 등 기본형에 대한 입출력 지원
- ObjectInput/OutputStream
	- 객체 단위 직렬화
-  Input,OutputStreamReader/Writer
	- 문자열 처리(인코딩)
- PrintWriter
	- 형식문자 지원

16진수 4비트 필요... utf는 한글표현에 0xFF이런식으로하니 8비트 특 1바이트가필요함

![[Pasted image 20250630075347.png]]