---
tags: 
created: 2025-07-28
---
alias 를 쓰면 특정 디렉토리 가는 명령어를 칠 수고를 덜 수 있다.

**현재 터미널 세션에만 적용하는 방식
**

`$ alias cdweb='cd /var/www/html'`

위 명령어는 cdweb 별칭이 cd 를 대응하는것임.

매번 설정하지 않고 로그인 시 자동으로 alias 적용하고 싶다면 쉘의 구성 파일에 등록하면 된다. bash의 경우 홈 디렉토리에 위치한 .bashrc 파일 하단에 alias 설정을 추가한다.

