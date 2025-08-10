---
tags: 
created: 2025-08-10
---


## TCP 기초

### TCP의 정의와 특징

- 연결지향 프로토콜
- 신뢰성 보장 메커니즘
- 전이중 통신(Full-Duplex)
- 바이트 스트림 서비스

## TCP 헤더 구조

### 2.1 TCP 헤더 분석

- 20바이트 기본 헤더 구조
- 각 필드별 상세 설명
    - Source/Destination Port
    - Sequence Number
    - Acknowledgment Number
    - Window Size
    - Checksum
    - Urgent Pointer

### 2.2 TCP 플래그 (Control Bits)

- SYN, ACK, FIN, RST, PSH, URG
- 각 플래그의 의미와 사용 시점

## 3장. TCP 연결 관리

### 3.1 3-Way Handshake

- 연결 설정 과정 상세 분석
- SYN → SYN+ACK → ACK
- 각 단계별 상태 변화
- 실패 시나리오와 대응

### 3.2 4-Way Handshake

- 연결 종료 과정
- FIN → ACK → FIN → ACK
- TIME_WAIT 상태의 의미
- 비정상 종료 (RST)

### 3.3 TCP 상태 다이어그램

- 11가지 TCP 상태 완벽 정리
- LISTEN, SYN_SENT, ESTABLISHED 등
- 상태 전환 조건과 시나리오

## 4장. TCP 신뢰성 보장 메커니즘

### 4.1 순서 보장 (Sequence Number)

- 시퀀스 번호 동작 원리
- 순서가 바뀐 패킷 처리
- 32비트 시퀀스 공간 관리

### 4.2 오류 검출과 복구

- Checksum을 통한 오류 검출
- 재전송 메커니즘
- 타임아웃과 RTT 계산

### 4.3 중복 제거

- 중복 패킷 감지와 처리
- ACK 번호 활용

## 5장. TCP 흐름 제어 (Flow Control)

### 5.1 윈도우 기반 흐름 제어

- Sliding Window 프로토콜
- Receive Window 크기 조절
- Zero Window와 Window Probe

### 5.2 버퍼 관리

- 송신/수신 버퍼의 역할
- 버퍼 오버플로우 방지

## 6장. TCP 혼잡 제어 (Congestion Control)

### 6.1 혼잡 제어 알고리즘

- **Slow Start**: 지수적 증가
- **Congestion Avoidance**: 선형 증가
- **Fast Retransmit**: 빠른 재전송
- **Fast Recovery**: 빠른 회복

### 6.2 혼잡 윈도우 (cwnd) 관리

- cwnd vs rwnd 차이점
- 혼잡 발생 감지 방법
- Additive Increase Multiplicative Decrease (AIMD)

### 6.3 최신 혼잡 제어 알고리즘

- TCP Reno, NewReno
- TCP Cubic (Linux 기본)
- TCP BBR (Google)

## 7장. TCP 성능 최적화

### 7.1 TCP 튜닝 파라미터

- 윈도우 크기 조정
- 버퍼 크기 최적화
- Nagle 알고리즘
- Delayed ACK

### 7.2 고속 네트워크 환경 대응

- TCP Window Scaling
- Selective ACK (SACK)
- TCP Timestamps

## 8장. TCP 실무 분석

### 8.1 패킷 캡처 분석

- Wireshark를 이용한 TCP 분석
- 실제 트래픽 패턴 해석
- 성능 병목 지점 찾기

### 8.2 일반적인 TCP 문제들

- Connection Timeout
- Packet Loss 대응
- 대역폭 지연 곱 (BDP) 계산

### 8.3 애플리케이션별 TCP 특성

- HTTP/HTTPS
- 데이터베이스 연결
- 실시간 스트리밍

## 9장. TCP 보안

### 9.1 TCP 공격 유형

- SYN Flooding
- TCP Hijacking
- Sequence Number 예측 공격

### 9.2 보안 대책

- SYN Cookies
- Random Sequence Number
- 방화벽과 IDS 연동

## 10장. 차세대 TCP

### 10.1 TCP 확장

- Multipath TCP (MPTCP)
- TCP Fast Open (TFO)
- TCP Mobile

### 10.2 QUIC과 UDP 기반 프로토콜

- HTTP/3과 QUIC
- TCP vs QUIC 비교
- 미래 전망

## 부록

### A. TCP RFC 문서 정리

### B. Linux TCP 설정 명령어 모음

### C. 프로그래밍 언어별 TCP 소켓 예제

### D. TCP 관련 도구 및 유틸리티

---

## 📝 학습 팁

- **각 장마다 Wireshark 실습 포함**
- **이론 → 실습 → 문제해결 순서로 학습**
- **네트워크 시뮬레이터로 시나리오 테스트**
- **실제 서비스 환경에서의 TCP 동작 관찰**