---
tags:
  - non-blocking-io
created: 2025-07-27
---
사용자가 폭발적으로 증가하면 어느 순간 경량 스레도도 한계가 옴. 이때는 서버의 IO 구현 방식을 구조적으로 변경해야한다. 바로 논블로킹 IO를 사용하는 것.

오래전부터 네트워크 서버 성능을 높이기 위해 사용한 방식. 여기에 비동기 API 곁들이면 덜 복잡한 코드로 높은 성능을 낼 수있다. Nginx, Netty, Node.js 등 서버에서 많이 사용하는 기술은 성능을 위해 논블로킹 IO를 쓰고있음.

## 논블로킹 IO 동작 개요
논블로킹 IO는 입출력이 끝날 때 까지 스레드가 대기 안한다. 다음 코드에서 channel.read() code는 데이터를 읽을 때 까지 대기 안함. channel.read() code는 읽을 데이터 없으면 바로 0 리턴. 이건 데이터 읽을 때까지 대기하는 블로킹 IO와는 동작방식이 다름!
```java
// channel: SocketChannel, buffer: ByteBuffer
int byteReads = channel.read(buffer);//데이터 읽을 때까지 대기 안함
...//읽은 데이터 없어도 다음 코드 실행 계속
```
논블로킹 IO는 데이터 조회를 보장안하기에 블로킹IO처럼 데이터 조회를 가정하고 코드 못짠다. 대신 루프 안에서 조회 반복해서 호출한 뒤 데이터 읽었을때만 처리하는 방식으로 구현 가능.

```java
while(true){
	int byteReads = channel.read(buffer);
	if(byteReads > 0){
		handleData(channel, buffer);
	}
}
```
그런데 이건 CPU 낭비 심함.

실제로 논블로킹 IO 사용시에는 데이터 읽기 바로 시도보다는 어떤 연산 수행할 수 있는지 확인하고 해당 연산 실행하는 방식으로 구현.

1. 실행 가능한 IO 연산 목록 구한다.(실행 가능한 연산 구할때까지 대기)
2. 1에서 구한 IO 연산 목록을 차례로 순회
	1. 각 IO 연산 처리.
3. 이 과정 반복.
다음은 이 방식으로 구현한 예제.

```java
Selector selector = Selector.open();

ServerSocketChannel serverSocket = ServerSocketChannel.open();
serverSocket.bind(new InetSocketAddress(7031));
serverSocket.configureBlocking(false); // 서버 소켓 비동기 설정

serverSocket.register(selector, SelectionKey.OP_ACCEPT); //연결 연산 등록

while(true){
	selector.select(); // 가능한 IO 연산이 있을 때 까지 대기
	Set<SelectionKey> selectedKeys = selector.selectedKey();
	Iterator<SelectionKey> itr = selectedKeys.iterator();
	while(iterator.hasNext()){//IO연산 순회
		SelectionKey key = iterator.next();
		iterator.remove();
		if(key.isAcceptable()) { // 클라와 연결 처리 가능하면
			SocketChannel client = serverSocket.accept();//클라 연결 처리
			client.configureBlocking(false);//소켓 비동기 설정
			client.register(selector, SelectionKey.OP_READ);//읽기 연산 등록
		}else if(ket.isReadable()){//읽기 연산 가능하면
			SocketChannel channel = (SocketChannel)ket.cahnnel();//채널 구함
			int readBytes = channel.read(inBuffer);//채널에 읽기 연산 실행
			int(readBytes == -1){
				channel.close();
			}
		}else{
			inBuffer.flip();
			outBuffer.put(inBuffer);//출력 버퍼에 복사
			inBuffer.clear();
			outBuffer.flip();
			channel.write(outBuffer)//채널에 쓰기 연산 실행
			outBuffer.clear();
		}
	}
}
```
여기서 핵심은 `Selector`. `Selector#select()` 는 IO 처리가 가능한 연산이 존재할 때까지 대기. 이 메서드가 리턴하면 수행 가능한 연산이 존재하는 것.

실행 가능한 연산 목록은 `Selector#selectedKeys()`로 조회하는데 이렇게 구한 SelectionKey 이용해서 어떤 연산이 가능한지 확인하고 해당 연산을 수행한다.

논블로킹을 1개 스레드로 구현하면 동시성 떨어짐. 앞선 예제 코드 기준으로 1개 채널에 대한 읽기 처리가 끝나야 다음 채널 읽기 실행함. 즉, 주 채널에 대한 읽기 연산이 가능하더라도 한 번에 1개의 채널에 대해서만 처리가 되는 것.

논블로킹 입출력에서 동시성 높이기 위해서 사용하는 방법은 채널들을 N개의 그룹으로 나누고, 각 그룹마다 스레드를 생성하는 것. 보통 CPU 개수 만큼 그룹 나누도 각 그룹마다 입출력을 처리할 스레드 할당.

## 리액터 패턴
논블로킹 IO 이용해서 구현할때 사용하는 패턴 중 하나. 논블로킹으로 구현된 네트워크 프레임워크 문서 읽다 보면 보이는 '리액터'가 이거 말함.

이 패턴은 동시에 들어오는 여러 ==이벤트를 처리하기 위한 이벤트 처리 방법==. 이 패턴은 크게 리액터와 핸들러 두 요소로 구성.

먼저 리액터는 이벤트가 발생할 때 까지 대기하다가 이벤트가 발생하면 알맞은 핸들러에 이벤트를 전달. 이벤트를 받은 핸들러는 필요한 로직을 수행함. 리액터는 다음과 유사한 형태 가짐
```java
while(isRunning){
	List<Event> events = getEvents();//이벤트 발생까지 대기
	for(Event event : events){
		Handler handler = getHandler(event);//이벤트 처리할 핸들러 구함
		handler.handle(event);//이벤트 처리
	}
}
```
이 코드 보면 리액터는 이벤트 대기하고 핸들러에 전달하는 과정을 반복함. 그래서 리액터를 ==이벤트 루프==라고도 한다.

앞서본 논블로킹 IO 코드 부분 참고하면
```java
...
while(true){
	selector.select(); // 가능한 IO 연산이 있을 때 까지 대기
	Set<SelectionKey> selectedKeys = selector.selectedKey();
	Iterator<SelectionKey> itr = selectedKeys.iterator();
	while(iterator.hasNext()){//IO연산 순회
		SelectionKey key = iterator.next();
		... key 타입따라 알맞은 처리
	}

```
이 코드에 SelectionKey를 이벤트에 대응하면 리액터 패턴과 완전히 방식이 동일한것 알 수 있다. 실제 논블로킹 IO에 기반한 Netty, Nginx, Node.js 등 서버는 리액터 패턴을 적용하고 있음.

리액터 패턴에서 이벤트 루프는 단일 스레드로 실행됨. 멀티 코어 가진 서버에서 단일 스레드만 쓰면 처리량 최대로 못 뽑아냄. 또 핸들러에서 CPU 연산이나 블로킹을 유발하는 연산 수행하면 그 시간만큼 전체 이벤트 처리 시간이 지연.

이런 한계 보완위해 핸들러나 블로킹 연산을 별도 스레드 풀에서 실행하기도 한다. 예를 들어 Netty는 여러개의 이벤트 루프를 생성해서 멀티 코어를 활용. Node.js는 이벤트 루프 외에 별도의 스레드 풀 이용해서 CPU 중심 작업이나 블로킹 연산을 동시처리함.

## 프레임워크 사용하기
줄 단위 데이터 수신하는 서버 구현한다고 가정. 블로킹IO 일경우 BufferedReader 사용해서 쉽게 줄 단위로 데이터 읽기가 가능하다.
```java
BufferedReader bf = new BufferedReader(new InputStreamReader(socket.getInputStream, "UTF-8"));

...

String line;
while((line = br.readLine()) != null){
	//line 처리
}
```
논블로킹 IO 쓰면 복잡해진다. 읽고나서 \n 있는지 확인하는 코드 구현해야됨. \n가 없는 경우 읽은 데이터를 별도 버퍼에 계속 누적 처리해야되는 로직도 필요. 또한 \n가 여러개 존재하는 경우도 처리해야됨. 채널마다 누적 처리 위한 버퍼 또한 관리.

다음은 클로드가 만들어준 예시 코드
```java
public class NonBlockingLineServer {
    private Map<SocketChannel, StringBuilder> clientBuffers = new HashMap<>();
    
    public void handleRead(SocketChannel channel) throws IOException {
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        int bytesRead = channel.read(buffer);
        
        if (bytesRead > 0) {
            buffer.flip();
            String data = new String(buffer.array(), 0, buffer.limit());
            
            // 기존 미완성 데이터와 합치기
            StringBuilder clientBuffer = clientBuffers.get(channel);
            if (clientBuffer == null) {
                clientBuffer = new StringBuilder();
                clientBuffers.put(channel, clientBuffer);
            }
            
            clientBuffer.append(data);
            
            // 완성된 줄들을 찾아서 처리
            String fullData = clientBuffer.toString();
            String[] lines = fullData.split("\n", -1);
            
            // 마지막 줄은 미완성일 수 있음
            for (int i = 0; i < lines.length - 1; i++) {
                processCompleteLine(lines[i]);
            }
            
            // 미완성 데이터는 다시 버퍼에 저장
            clientBuffer.setLength(0);
            clientBuffer.append(lines[lines.length - 1]);
        }
    }
}
```

주고받는 데이터형식 조금만 바뀌어도 저수준의 IO 처리 코드를 변경해야됨. 이런 저수준을 직접 구현하는것보다 프레임워크를 쓰고 나머지 시간에는 비즈니스로직에 집중하자.

다음은 책의 예제코드. Netty를 쓴거임. 줄 단위로 데이터 주고 받는 에코서버.

```java
DisposableServer server = TcpServer.create();
	.port(2132)
	.doOnConnection(conn -> conn.addHandlerFirst(new LineBasedFrameDecoder(1024)))//줄단위 읽기 처리
	.handle((in,out)->{
		return in.receive()
			.asString()//byte를 문자열로 반환
			.doOnNext(line -> {
				log.info("received: {}", line);
			})
			.flipMap(line -> out.sendString(Mono.just(line + "\n))//문자열 쓰기
			);
	})
	.bindNow();
```
Reactor Netty 가 줄 단위 읽기와 문자열 반환 처리 기능 제공하니 저수준의 IO 처리를 직접 구현안해도 됨.
