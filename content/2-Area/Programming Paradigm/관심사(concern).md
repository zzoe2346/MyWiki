---
created: 2025-06-22
---
소프트웨어에서 하나의 모듈이나 클래스가 담당해야하는 역할을 명확하게 분리하는 것

- 예를 들어 "이벤트 예약 시스템에서"다음과 같은 관심사 존재
	- 예약 처리(예약 데이터 저장, 좌석 확인 등)
	- 결제 처리(결제 시스템 연동, 승인/취소 등)
	- 알림 처리(카톡, 이메일, SMS 등)
- 관심사 미 분리시, 하나의 서비스에서 모든것 처리해야 하므로 코드가 점점 복잡해지고 유지보수가 어려워짐