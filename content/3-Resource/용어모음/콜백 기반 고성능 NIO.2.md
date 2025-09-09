---
created: 2025-07-01
tags:
  - cs/os/term
---
**NIO vs NIO.2**


| NIO(Java 1.4)                      | NIO.2(AIO, Java 7)                                         |
| ---------------------------------- | ---------------------------------------------------------- |
| Multiplexing I/O                   | CompletionHandler(Asynchronous IO)                         |
| Single Thread                      | Thread pool(OS수준 통제)                                       |
| SocketChannel, ServerSocketChannel | AsynchronousSocketChannel, AsynchronousServerSocketChannel |
참고로 비교표 아님!

변화를 캐치해서 그걸 이벤트화하는게 NIO의 핵심

IO 수행자는 운영체제...고성능 내려면 운영체제 서포트를 받아야됨

운체야 너 잘하니까 나한테 결과만 통지해줘!