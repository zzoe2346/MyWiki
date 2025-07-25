---
created: 2025-07-12
---
- 유티코드 코드 포인트를 16비트 단위로 인코딩하는 **가변길이** **인코딩**
	- 16비트 1개 혹은 2개 써서 하나의 유니코드 문자 표현
	- 2바이트 혹은 4바이트
- 유니코드 BMP(Basic Multilingual Plane)에 속한 문자는 코드 포인트 값 그대로 사용하므로 직관적
- BMP 벗어나는 문자들(이모지, 특수 한자, 고대 문자 등)은 16비트 두개를 Surrogate Pair로 묶어 표현
- [[Endian]]의 영향을 받기에 구분이 필요하며 BOM(byte order mark)이 사용될수도 있음

## UTF-16 LE vs UTF-16 BE
- LE는 Little endian, BE는 Big endian
	- OxAC00(가) 경우, 
		- LE: 00 AC
		- BE: AC 00
	- Java는 내부적으로 UTF-16 BE 씀
- [[BOM]]
	- U+FEFF(BE)
	- U+FFFE(LE)
	- 항상 포함되는건 아니지만 대부분 경우 포함시킴(윈도우 메모장은 항상 포함)
**Little Endian**
![[Pasted image 20250712230036.png]]
**Big Endian**
![[Pasted image 20250712230054.png]]