---
created: 2025-05-02
---
네, 나머지 세 가지 I/O 방식에 대한 예제 코드를 Java를 사용하여 만들어 보겠습니다. 각 예제는 간략하게 I/O 작업을 시뮬레이션하고, 각 방식의 특징을 보여주는 데 초점을 맞출 것입니다.

**1. 비동기 논블로킹 I/O (Asynchronous Non-blocking I/O)**

이 방식은 I/O 요청을 시작한 후 즉시 반환되며, I/O 작업이 완료되면 콜백 함수나 이벤트 핸들러를 통해 결과를 통지받습니다. 스레드는 I/O 작업이 진행되는 동안 다른 작업을 수행할 수 있으며, 결과를 기다리는 동안 블로킹되지 않습니다. Java NIO.2 (Asynchronous I/O) API를 사용하여 구현합니다.

 

```java
import java.nio.ByteBuffer;
import java.nio.channels.AsynchronousFileChannel;
import java.nio.channels.CompletionHandler;
import java.nio.file.Path;
import java.nio.file.StandardOpenOption;
import java.util.concurrent.Future;

public class AsyncNonBlockingIOExample {
    public static void main(String[] args) throws Exception {
        Path file = Path.of("async_nonblocking_test.txt");
        try (AsynchronousFileChannel fileChannel = AsynchronousFileChannel.open(file, StandardOpenOption.READ)) {
            ByteBuffer buffer = ByteBuffer.allocate(1024);
            long position = 0;

            // CompletionHandler를 사용하여 I/O 작업 완료 시 콜백 처리
            CompletionHandler<Integer, Void> handler = new CompletionHandler<Integer, Void>() {
                @Override
                public void completed(Integer result, Void attachment) {
                    if (result > 0) {
                        buffer.flip();
                        byte[] data = new byte[buffer.remaining()];
                        buffer.get(data);
                        System.out.println("비동기 논블로킹 읽기 완료: " + new String(data));
                    } else {
                        System.out.println("파일 끝에 도달했습니다.");
                    }
                }

                @Override
                public void failed(Throwable exc, Void attachment) {
                    System.err.println("비동기 논블로킹 읽기 실패: " + exc.getMessage());
                }
            };

            System.out.println("비동기 논블로킹 읽기 요청 시작...");
            fileChannel.read(buffer, position, null, handler);

            // 메인 스레드는 I/O 작업 완료를 기다리지 않고 다른 작업을 수행할 수 있습니다.
            System.out.println("메인 스레드는 다른 작업을 수행 중...");
            Thread.sleep(5000); // 5초 동안 다른 작업 시뮬레이션

            // 또는 Future를 사용하여 결과를 기다릴 수도 있습니다.
            ByteBuffer buffer2 = ByteBuffer.allocate(1024);
            Future<Integer> future = fileChannel.read(buffer2, 0);
            System.out.println("비동기 논블로킹 읽기 요청 (Future) 시작...");
            // future.get()을 호출하면 작업이 완료될 때까지 블로킹됩니다 (비동기 블로킹 I/O와 유사).
            // 여기서는 논블로킹 예시이므로 get()을 바로 호출하지 않습니다.
            System.out.println("메인 스레드는 Future를 통해 결과를 기다리지 않고 계속 작업...");
            Thread.sleep(3000);
            if (future.isDone()) {
                int result2 = future.get();
                if (result2 > 0) {
                    buffer2.flip();
                    byte[] data2 = new byte[buffer2.remaining()];
                    buffer2.get(data2);
                    System.out.println("비동기 논블로킹 읽기 완료 (Future): " + new String(data2));
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

**2. 동기 블로킹 I/O (Synchronous Blocking I/O)**

가장 기본적인 I/O 방식입니다. I/O 요청을 하면 작업이 완료될 때까지 스레드는 블로킹되어 아무런 작업을 수행하지 못합니다. Java의 `InputStream`과 `OutputStream` 등의 기본적인 I/O 스트림이 이 방식을 사용합니다.

 

```java
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;

public class SyncBlockingIOExample {
    public static void main(String[] args) {
        try (InputStream inputStream = new FileInputStream("sync_blocking_test.txt")) {
            System.out.println("동기 블로킹 읽기 시작...");
            int data;
            while ((data = inputStream.read()) != -1) {
                System.out.print((char) data);
            }
            System.out.println("\n동기 블로킹 읽기 완료.");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

**3. 동기 논블로킹 I/O (Synchronous Non-blocking I/O)**

이 방식에서는 I/O 요청을 하면 즉시 반환됩니다. 그러나 I/O 작업이 완료되었는지 여부를 확인하기 위해 **반복적으로 시스템 호출을 해야 합니다 (폴링)**. 데이터가 준비되지 않았으면 다시 시도해야 하므로, CPU를 비효율적으로 사용할 수 있습니다. Java NIO (New I/O)의 채널과 셀렉터를 사용하여 구현합니다.

 

```java
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SocketChannel;

public class SyncNonBlockingIOExample {
    public static void main(String[] args) {
        try (SocketChannel socketChannel = SocketChannel.open()) {
            socketChannel.configureBlocking(false); // 논블로킹 설정
            InetSocketAddress address = new InetSocketAddress("localhost", 8080);

            System.out.println("동기 논블로킹 연결 시도...");
            socketChannel.connect(address);

            // 연결이 완료될 때까지 폴링
            while (!socketChannel.finishConnect()) {
                System.out.println("연결 시도 중...");
                Thread.sleep(100); // 짧은 간격으로 재시도
            }
            System.out.println("연결 성공!");

            ByteBuffer buffer = ByteBuffer.allocate(1024);
            String message = "Hello from non-blocking client!";
            buffer.put(message.getBytes());
            buffer.flip();
            System.out.println("동기 논블로킹 쓰기 시도...");
            while (buffer.hasRemaining()) {
                socketChannel.write(buffer);
            }
            System.out.println("동기 논블로킹 쓰기 완료.");

            ByteBuffer readBuffer = ByteBuffer.allocate(1024);
            System.out.println("동기 논블로킹 읽기 시도...");
            int bytesRead = socketChannel.read(readBuffer);
            if (bytesRead > 0) {
                readBuffer.flip();
                byte[] data = new byte[readBuffer.remaining()];
                readBuffer.get(data);
                System.out.println("동기 논블로킹 읽기 완료: " + new String(data));
            } else if (bytesRead == 0) {
                System.out.println("읽을 데이터 없음.");
            } else if (bytesRead == -1) {
                System.out.println("연결 종료.");
                socketChannel.close();
            }

        } catch (IOException | InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

**참고:**

- 비동기 논블로킹 I/O 예제는 파일 I/O를 대상으로 했으며, 콜백 (`CompletionHandler`)과 `Future`를 사용하는 두 가지 방식을 간략히 보여줍니다. 실제 네트워크 프로그래밍에서는 셀렉터와 함께 사용하여 여러 채널의 I/O 이벤트를 효율적으로 처리합니다.
- 동기 논블로킹 I/O 예제는 소켓 채널을 사용하여 연결 시도, 쓰기, 읽기 작업을 논블로킹 방식으로 수행하는 것을 보여줍니다. 연결 및 읽기 작업 완료 여부를 반복적으로 확인하는 폴링 방식이 특징입니다.

이 예제 코드를 통해 각 I/O 방식의 기본적인 동작 방식을 이해하는 데 도움이 되셨기를 바랍니다. 각 방식은 특정 상황과 요구 사항에 따라 장단점을 가지므로, 시스템의 특성과 성능 요구 사항을 고려하여 적절한 I/O 방식을 선택하는 것이 중요합니다. 😊