---
created: 2025-07-01
tags:
  - cs/os/term
---
`java.nio.channels.FileChannel`
- 파일 입출력을 매핑된 버퍼 통해 쉽고 효율적으로 처리할 수 있도록 지원
	- 랜덤 액세스 지원(Read/Write/Seek)
	- 메모리 매핑 파일 지원(파일을 메모리로 추상화)
- 기존 FileInput/OutputStream 보다 빠르고 유연
- 기존 파일 스트림에 대해 채널을 생성해 사용하는 구조
- 스트림에 대한 채널...
	- 스트림의 확장?개선?이라 생각해보자
## FileChannel 형제관계 클래스
`java.nio.channels.spi,AbstractInterruptibleChannel`
**소켓관련**
- SocketChannel
- ServerSocketChannel
- DatagramChannel
Pipe관련
- Pipe.SourceChannel
- Pipe.SinkChannel
