---
created: 2025-06-11
tags:
  - TCP
  - 네트워크
---
1. SYN: 클라이언트는 서버에 클라이언트의 ISN 담아 SYN 보냄
2. SYN + ACK: 서버는 클라이언트의 SYN 수신하고 서버의 ISN 을 보내며 승인번호로 클라이언트의 ISN + 1 보냄
3. ACK: 클라이언트는 서버의 ISN + 1 한 값인 승인번호를 담아 ACK를 서버에 보냄

- 양쪽 모두 데이터 주고 받을 준비 완료 보장 위함
> ISN: TCP 기반 데이터 통신에서 각각의 새 연결에 할당된 고유한 32비트 시퀀스 번호. TCP 연결 통해 전송되는 다른 데이터 바이트와 충돌하지 않는 시퀀스 번호를 할당하는데 도움이 됨

> SYN: synchronization 약자, 연결 요청 플래그, synchronize sequence numbers

> ACK: acknowledgement 약자, 응답 플래그

- SYN 을 보내는 쪽은 본인 ISN을 보내고 받는애들은 ACK를 반환하는데 여기에 SYN에 있던 ISN++을 넣어 보냄

![[Pasted image 20250611080536.png]]