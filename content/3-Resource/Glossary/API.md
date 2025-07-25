---
created: 2025-04-09
---

Application Programming **Interface**는 둘 이상의 컴퓨터 프로그램이 서로 통신하는 방법. 

## 장점
1. 제공자는 서비스의 중요한 부분 숨길 수 있음.
	- DB설계 구조나 숨기고 싶은 테이블 정보, 서버의 상수값을 감추고 주고싶은 부분만 주기 가능.
2. 사용자는 해당 서비스가 어떻게 구현되는지 알 필요없이 필요한 정보만 얻을 수 있음.
3. 오픈 API의 경우 개발 프로세스 단축, 단순화 -> 시간, 비용 절감
	 - 네이버 로그인 등
4. 내부 프로세스 수정 시, API를 매번 수정안해도 됨. 내부 로직이 변경되어도 사용자는 그냥 쓰면 되는것. 
5. 제공자는 데이터를 한곳에 모을 수 있음. 특정한것 클릭시 이를 집게하는 API를 호출하게 해서 관련 정보를 수집하는 것.
## 종류
- private: 내부적으로 사용. 주로 해시키를 하드코딩해놓고 이를 기반으로 서버와 서버간의 통신. 이는 비즈니스 파트너와도 해당.
- public: 모든 사람이 사용하도록 허용. 트래픽 관리를 위해 하루 요청수의 제한, 계정당 몇개 등으로 관리.