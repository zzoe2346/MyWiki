---
tags:
  - non-blocking-io
created: 2025-07-27
---
사용자가 폭발적으로 증가하면 어느 순간 경량 스레드도 한계가 옴. 이때는 서버의 IO 구현 방식을 구조적으로 변경해야한다. 바로 **논블로킹 IO**를 사용하는 것.

오래전부터 네트워크 서버 성능을 높이기 위해 사용한 방식. 여기에 비동기 API 곁들이면 덜 복잡한 코드로 높은 성능을 낼 수있다. Nginx, Netty, Node.js 등 서버에서 많이 사용하는 기술은 성능을 위해 논블로킹 IO를 쓰고있음.

## 논블로킹 IO 동작 개요
==논블로킹 IO는 입출력이 끝날 때 까지 스레드가 대기 안한다==. 다음 코드에서 `channel.read()` code는 데이터를 읽을 때 까지 대기 안함. `channel.read()` code는 읽을 데이터 없으면 바로 0 리턴. 이건 데이터 읽을 때까지 대기하는 블로킹 IO와는 동작방식이 다름!
```
// channel: SocketChannel, buffer: ByteBuffer
int byteReads = channel.read(buffer);//데이터 읽을 때까지 대기 안함
...//읽은 데이터 없어도 다음 코드 실행 계속
```
논블로킹 IO는 데이터 조회를 보장안하기에 블로킹IO처럼 데이터 조회를 가정하고 코드 못짠다. 대신 루프 안에서 조회 반복해서 호출한 뒤 데이터 읽었을때만 처리하는 방식으로 구현 가능.

```java
//cpu 낭비 심함
while(true){
	int byteReads = channel.read(buffer);
	if(byteReads > 0){
		handleData(channel, buffer);
	}
}
```
그런데 이건 CPU 낭비 심함.

==실제로 논블로킹 IO 사용시에는 데이터 읽기 바로 시도보다는 어떤 연산 수행할 수 있는지 확인하고 해당 연산 실행하는 방식으로 구현.==

1. 실행 가능한 IO 연산 목록 구한다.(실행 가능한 연산 구할때까지 대기)
2. 1에서 구한 IO 연산 목록을 차례로 순회
	1. 각 IO 연산 처리.
3. 이 과정 반복.
다음은 이 방식으로 구현한 예제.

Selector를 써야함!!!

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
여기서 핵심은 `Selector`. `Selector#select()` 는 **IO 처리가 가능한 연산이 존재할 때까지 대기**. 이 메서드가 리턴하면 수행 가능한 연산이 존재하는 것.

실행 가능한 연산 목록은 `Selector#selectedKeys()`로 조회하는데 이렇게 구한 SelectionKey 이용해서 어떤 연산이 가능한지 확인하고 해당 연산을 수행한다.

논블로킹 IO로 구현한 서버는 블로킹 IO이용한 구현과 차이가 난다. 일반적으로 블로킹 IO로 구현한 서버는 커넥션별(또는 요청별로) 스레드 할당한다. 동시 연결 클라가 1000개면 처리할 스레드 1000개를 생성. 반면 논블로킹 IO는 클라 수 상관없이 소수의 스레드 쓴다. 요청이 ㅈㄴ와도 한꺼번에 가져와서 소수의 스레드가 처리만하면되는거임.
### IO 멀티플렉싱
우리말로 IO 다중화.

단일 이벤트 루프에서 여러 IO 작업 처리한는 개념 표현할때 사용. 앞서 본 논블로킹 IO와 Selector 이용한 입출력 처리가 IO 멀티플렉싱에 해당.
 
OS에 따라 epoll(linux), IOCP(windows) 등 사용해서 구현. 더 적은 자원으로 더 많은 클라이언트 처리할 할수있어서 대규모 트래픽 처리해야되는 서버 구현시 많이씀.


논블로킹을 1개 스레드로 구현하면 동시성 떨어짐. 앞선 예제 코드 기준으로 1개 채널에 대한 읽기 처리가 끝나야 다음 채널 읽기 실행함. 즉, 두 채널에 대한 읽기 연산이 가능하더라도 한 번에 1개의 채널에 대해서만 처리가 되는 것.

논블로킹 입출력에서 동시성 높이기 위해서 사용하는 방법은 채널들을 N개의 그룹으로 나누고, **각 그룹마다 스레드를 생성하는 것**. 보통 CPU 개수 만큼 그룹 나누도 각 그룹마다 입출력을 처리할 스레드 할당.

셀렉터 구독자가 한명씩 있어야되는거임. 셀렉터가 IO 완료를 알아차리면 이걸 구독자 스레드한테 일하라고 시킨다.

---
네, 질문의 핵심을 아주 정확하게 파악하고 계십니다! Java의 `Selector`와 논블로킹 I/O의 동작 방식에 대해 물어보신 내용을 조금 더 구체적이고 정확하게 풀어 설명해 드리겠습니다.

결론부터 말씀드리면, **"네, 거의 맞습니다. Selector는 OS가 제공하는 고성능 I/O 이벤트 통지 기능(Linux의 epoll, Windows의 IOCP 등)을 활용하여, '지금 즉시 데이터를 읽거나 쓸 수 있는' 소켓 채널들을 알아내고, 애플리케이션에게 알려주는 역할을 합니다."**

이 과정을 단계별로 나누어 자세히 살펴보겠습니다.

### Java 논블로킹 I/O와 Selector의 동작 원리

전통적인 블로킹 I/O는 `read()`를 호출하면 데이터가 들어올 때까지 스레드가 무한정 대기(block)하는 방식이었습니다. 이는 스레드 낭비가 심각했죠. 논블로킹 I/O는 이 문제를 해결하기 위해 등장했습니다.

**1. 등록 (Registration): 감시 대상 추가**

-   먼저, 모든 소켓 통신은 `Channel`(e.g., `ServerSocketChannel`, `SocketChannel`) 객체를 통해 이루어집니다.
-   이 채널들을 **논블로킹 모드**로 설정합니다. (`channel.configureBlocking(false);`)
-   그리고 `Selector` 객체를 하나 생성한 뒤, 감시하고 싶은 채널들을 `Selector`에 등록합니다.
-   등록할 때는 어떤 I/O 이벤트에 관심이 있는지 함께 알려줍니다.
    -   `SelectionKey.OP_ACCEPT`: 클라이언트의 연결 요청
    -   `SelectionKey.OP_READ`: 데이터를 읽을 수 있는 상태
    -   `SelectionKey.OP_WRITE`: 데이터를 쓸 수 있는 상태
    -   `SelectionKey.OP_CONNECT`: 연결이 성공적으로 완료된 상태

**2. 대기 (Waiting/Selection): 이벤트 발생을 기다림**

-   애플리케이션의 메인 루프에서는 `selector.select()` 메소드를 호출합니다.
-   이 `select()` 메소드가 바로 핵심입니다. 이 메소드가 호출되면, **애플리케이션 스레드는 블로킹(대기) 상태에 들어갑니다.**
-   하지만 이 대기는 옛날처럼 하나의 I/O를 무작정 기다리는 것이 아닙니다. **등록된 여러 채널 중 단 하나라도 I/O 이벤트가 발생할 때까지** 효율적으로 기다립니다.
-   이 효율적인 대기가 바로 OS의 도움이 필요한 부분입니다.

**3. 이벤트 통지 (Event Notification): OS의 역할 (IOCP, epoll)**

-   질문에서 언급하신 **IOCP(I/O Completion Port)**는 Windows에서 사용하는 메커니즘입니다. Linux에서는 **epoll**, macOS에서는 **kqueue**라는 유사한 고성능 메커니즘을 사용합니다.
-   Java의 `Selector`는 **이러한 OS 네이티브 기능을 추상화한 API**입니다. JVM은 실행되는 OS를 감지하고, 그에 맞는 가장 효율적인 네이티브 API(epoll, kqueue, IOCP 등)를 내부적으로 호출합니다.
-   `selector.select()`가 호출되면, 실제로는 내부적으로 `epoll_wait()` (Linux)나 `GetQueuedCompletionStatus()` (Windows) 같은 OS 커널 함수가 호출됩니다.
-   ==OS 커널은 하드웨어(네트워크 카드)로부터 데이터가 도착하거나, 보낼 준비가 되었다는 신호를 받으면, 이벤트를 대기 중인 `Selector`에게 알려줍니다.==
	- 데이터가 도착하거나 보낼준비(커넥션?) 등이 준비완료되었을때 이를 셀렉터에 알린다.
-   이벤트가 발생하면 OS는 `selector.select()`를 대기 상태에서 깨워줍니다.

**4. 처리 (Processing): 준비된 I/O 작업 수행**

-   `selector.select()` 메소드가 반환되면(깨어나면), 이는 "준비된 채널이 있다"는 뜻입니다.
-   애플리케이션은 `selector.selectedKeys()`를 호출하여 **이벤트가 발생한 채널들의 목록(`SelectionKey`의 집합)을** 얻습니다.
-   이제 이 목록을 순회하면서 각 `SelectionKey`에 해당하는 채널에서 I/O 작업을 수행합니다.
    -   `key.isAcceptable()`: 새로운 클라이언트 연결이므로 `serverSocketChannel.accept()` 호출
    -   `key.isReadable()`: 읽을 데이터가 있으므로 `socketChannel.read()` 호출
    -   `key.isWritable()`: 데이터를 쓸 준비가 되었으므로 `socketChannel.write()` 호출
-   중요한 점은, 이 시점에서 호출하는 `read()`, `write()`, `accept()` 메소드들은 **절대 블로킹되지 않습니다.**(블로킹 안되고 CPU 팍팍쓰는구만) 왜냐하면 `Selector`가 이미 "작업할 준비가 되었다"고 알려준 채널들이기 때문입니다.
-   처리가 끝난 `SelectionKey`는 반드시 집합에서 제거해주어야 다음 이벤트를 정상적으로 받을 수 있습니다. (`iterator.remove()`)

### "I/O 완료" vs "I/O 준비" (IOCP와 epoll의 미묘한 차이)

질문에서 "IO가 완료된 걸 알아차린다"고 하셨는데, 이는 매우 중요한 지점입니다. 사실 여기에는 두 가지 모델이 있습니다.

1.  **Proactor 패턴 (I/O 완료 통지)**: Windows의 **IOCP**가 대표적입니다.
    -   OS에게 "이 버퍼에 데이터를 읽어줘"라고 요청하면, OS가 백그라운드에서 **데이터 읽기까지 완전히 끝낸 후** "너가 요청한 읽기 작업이 이 버퍼에 완료되었어"라고 통지해줍니다.
    -   애플리케이션은 I/O 작업 자체에 관여하지 않고, 완료 통보만 받아 처리합니다.

2.  **Reactor 패턴 (I/O 준비 통지)**: Linux의 **epoll**, 그리고 **Java의 `Selector`**가 따르는 기본 모델입니다.
    -   OS에게 "이 채널에 읽을 데이터가 생기면 알려줘"라고 요청합니다.
    -   OS는 데이터가 도착해서 **"이제 `read()`를 호출해도 바로 읽을 수 있는 상태가 되었어"라고 준비 상태를 통지**해줍니다.
    -   **실제 `read()` 호출을 통해 데이터를 버퍼로 가져오는 것은 애플리케이션의 역할**입니다.

따라서 조금 더 정확하게 말하면, Java의 `Selector`는 "I/O가 **완료**된 것"을 알아차리기보다는 "**I/O를 수행할 준비가 된 것**"을 알아차리고, 그 목록을 애플리케이션에게 넘겨주는 방식(Reactor 패턴)으로 동작합니다.

### 정리

-   **Java `Selector`는 OS가 제공하는 고성능 이벤트 통지 기능(epoll, kqueue, IOCP 등)을 추상화한 것입니다.**
-   `Selector`는 **"I/O 준비(Readiness)"** 이벤트를 감지합니다. (Reactor 패턴)
-   단 하나의 스레드가 `selector.select()`를 통해 여러 채널의 I/O 이벤트를 효율적으로 기다릴 수 있습니다.
-   이벤트가 발생하면 `select()`는 깨어나고, 애플리케이션은 **준비된 채널들**에 대해서만 논블로킹 방식으로 I/O 작업을 수행합니다.
-   이를 통해 적은 수의 스레드로 수많은 클라이언트 연결을 동시에 처리할 수 있는 고성능 서버를 구현할 수 있습니다. (e.g., Netty, Vert.x 같은 프레임워크의 핵심 원리)
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
Selector selector = Selector.open();

...생략

while(true){
	selector.select(); // 가능한 IO 연산이 있을 때 까지 대기
	Set<SelectionKey> selectedKeys = selector.selectedKey();
	Iterator<SelectionKey> itr = selectedKeys.iterator();
	while(iterator.hasNext()){//IO연산 순회
		SelectionKey key = iterator.next();
		... key 타입따라 알맞은 처리
	}

```
**이 코드에 SelectionKey를 이벤트에 대응하면 리액터 패턴과 완전히 방식이 동일한것 알 수 있다.** 실제 논블로킹 IO에 기반한 Netty, Nginx, Node.js 등 서버는 리액터 패턴을 적용하고 있음.

리액터 패턴에서 이벤트 루프는 단일 스레드로 실행됨. 멀티 코어 가진 서버에서 단일 스레드만 쓰면 처리량 최대로 못 뽑아냄. 또 핸들러에서 CPU 연산이나 블로킹을 유발하는 연산 수행하면 그 시간만큼 전체 이벤트 처리 시간이 지연.

**이런 한계 보완위해 핸들러나 블로킹 연산을 별도 스레드 풀에서 실행하기도 한다**.(이벤트루프로 다른 스레드가 핸들러의 연산, 블로킹 연산하도록 전달한다고 이해했다 지금) 예를 들어 Netty는 여러개의 이벤트 루프를 생성해서 멀티 코어를 활용. Node.js는 이벤트 루프 외에 별도의 스레드 풀 이용해서 CPU 중심 작업이나 블로킹 연산을 동시처리함.

---

네, 정확하게 이해하셨습니다! 그 개념이 바로 현대적인 비동기/논블로킹 프레임워크의 핵심 동작 방식입니다.

"이벤트 루프는 이벤트가 오면 이걸 다른 스레드로 넘긴다"는 이해는 **핵심적으로 맞습니다**. 다만, 모든 이벤트를 넘기는 것이 아니라 **"오래 걸리거나 블로킹될 가능성이 있는 작업"**을 선별해서 넘긴다는 점을 추가하면 완벽한 이해가 됩니다.

이 구조를 "두 개의 분업화된 팀"으로 비유하면 아주 쉽게 이해할 수 있습니다.

### 1. 이벤트 루프 스레드 (고객 응대 전문 '접수 데스크')

-   **역할**: 이 스레드의 유일하고 가장 중요한 임무는 **절대 멈추지 않고(Non-Blocking) 최대한 빨리 I/O 이벤트를 받아 처리하는 것**입니다.
-   **특징**:
    -   **단일 스레드 (또는 소수)**: 여러 스레드가 동시에 I/O를 기다릴 필요가 없으므로, 스레드 간의 비싼 컨텍스트 스위칭 비용이 발생하지 않습니다.
    -   **고객 응대 전문가**: 외부로부터 오는 모든 요청(연결, 데이터 수신 등)을 가장 먼저 받습니다.
    -   **빠른 판단**: 받은 요청이 무엇인지(이벤트 종류)를 신속하게 판단합니다.
        -   **간단한 작업 (1초 미만)**: 예를 들어, 요청 헤더를 파싱하거나, 간단한 메시지를 즉시 회신하는 등 금방 끝나는 작업은 자기가 직접 처리합니다. 데스크에서 바로 해결 가능한 문의와 같습니다.
        -   **복잡하고 오래 걸리는 작업**: DB 조회, 복잡한 계산, 외부 API 호출 등 시간이 걸리는 작업은 직접 처리하지 않습니다. 만약 직접 처리하면, 그 시간 동안 다른 모든 고객(이벤트)들이 하염없이 기다려야 하기 때문입니다.

### 2. 워커 스레드 풀 (업무 처리 전문 '실무팀')

-   **역할**: 이벤트 루프로부터 전달받은 **시간이 오래 걸리는 실제 비즈니스 로직을 실행**하는 전담팀입니다.
-   **특징**:
    -   **다중 스레드 (멀티 코어 활용)**: CPU 코어 수에 맞춰 여러 개의 스레드를 보유하고 있습니다. 이를 통해 여러 개의 복잡한 작업을 **동시에 병렬로 처리**하여 서버의 전체 처리량을 극대화합니다.
    -   **블로킹 OK**: 이 스레드들은 블로킹되어도 괜찮습니다. 하나의 워커 스레드가 DB 조회 때문에 10초간 멈춰있더라도, 다른 워커 스레드들은 다른 작업을 계속 처리할 수 있습니다. 가장 중요한 것은, 이들이 멈춰있는 동안 **이벤트 루프는 전혀 영향을 받지 않고** 계속해서 새로운 요청을 받고 있다는 점입니다.

### 전체 작업 흐름 (Netty, Node.js의 방식)

1.  **[이벤트 루프]**: 클라이언트로부터 데이터 수신 이벤트(`OP_READ`)를 감지합니다.
2.  **[이벤트 루프]**: 소켓에서 데이터를 읽습니다. (이 작업 자체는 매우 빠릅니다)
3.  **[이벤트 루프]**: 읽은 데이터를 분석해보니, DB에서 사용자 정보를 가져와 특정 계산을 해야 하는 복잡한 작업이라는 것을 파악합니다.
4.  **[핸드오프]**: 이벤트 루프는 이 작업을 직접 수행하지 않고, 작업에 필요한 데이터(e.g., 사용자 ID)를 **'작업 객체(Task)'로 감싸서 워커 스레드 풀의 작업 큐(Task Queue)에 넣습니다.** 그리고 자신은 즉시 다음 이벤트를 받으러 갑니다.
5.  **[워커 스레드]**: 워커 스레드 풀에서 놀고 있던 스레드 하나가 큐에 들어온 작업을 가져가 실행합니다.
    -   데이터베이스에 연결하고 쿼리를 날립니다. (이때 이 워커 스레드는 블로킹될 수 있습니다)
    -   DB로부터 결과를 받아 복잡한 계산을 수행합니다.
6.  **[결과 전달]**: 작업이 완료되면, 워커 스레드는 그 결과를 다시 **이벤트 루프의 큐에 넣어줍니다.** "클라이언트 A에게 이 결과를 전송해달라"는 새로운 요청의 형태로 전달합니다.
7.  **[이벤트 루프]**: 이벤트 루프는 자신의 큐에 들어온 '전송 요청'을 보고, 해당 클라이언트의 채널(소켓)에 응답 데이터를 써줍니다. (이 쓰는 작업 `write()` 역시 논블로킹으로 빠르게 처리됩니다)

---

### 결론: 왜 이 구조가 강력한가?

-   **역할 분리 (Separation of Concerns)**: I/O를 처리하는 부분과 비즈니스 로직을 처리하는 부분을 명확히 분리했습니다.
-   **이벤트 루프의 반응성 유지**: 가장 중요한 이벤트 루프가 절대 블로킹되지 않으므로, 서버는 항상 새로운 요청에 즉각적으로 반응할 수 있습니다. (Low Latency)
-   **멀티 코어의 효율적 활용**: CPU를 많이 사용하는 계산 작업들은 워커 스레드 풀에서 병렬로 처리되므로, 서버의 전체 처리량(Throughput)이 극대화됩니다.

따라서 사용자께서 이해하신 **"이벤트 루프는 이벤트를 받아 (오래 걸리는 일은) 다른 스레드로 넘긴다"**는 통찰은 현대 비동기 서버 아키텍처의 정수를 꿰뚫는 정확한 표현입니다.

---

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
