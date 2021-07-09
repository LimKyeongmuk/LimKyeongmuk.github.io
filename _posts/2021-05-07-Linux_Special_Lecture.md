---
title: "리눅스 특강"
excerpt: ""

categories:
  - Linux
tags:
  - Linux
  - UNIX
---
김선영 강사
금융시스템 쪽에서 업무

양이 많아서 기억에 남지 않는 부분이 많음
한번이라도 실습을 하면 기억이 남(실습에 의의를 두기)

기초임에도 너무 양이 많습니다
완전히 기초라고 보기엔 힘들다

이번 강의는 ros보다 system, server 쪽 분야

리눅스를 배우는 목적 자체가 ROS만이 목적은 아님

사회에서는 대부분이 리눅스 환경

시스템을 잘 알고있으면 튜닝할 때 좋음

자율주행 뿐만 아니라 알면 앞으로 도움이 많이됨

잘못배우면 기본기가 많이 떨어질 수 있음

주문시스템 자동화 프로그램을 짜서 50억 정도 주문이 잘못나갔음

가상머신이 아닌 실제 리눅스를 사용하는 것이 중요

실무에서 리눅스
- RH 계열: server, 대형 머신, 웹서버, 공항시스템(안정성 측면이 더 높음)
- Debain 계열: IoT, 라즈베리(안정성이 조금 떨어짐)
자율주행 같은 경우는 우분투 계열을 많이 사용함(초반이기 때문에, 나중에 어떻게 바뀔지 모름)
  
리눅스 중요한 부분
1. 역사
- C 언어와 같이 성장
- 국제표준(POSIX), X/Open, SVR4, BSD, SUS
- 유닉스 역사책
2. 명령어
- 외우려고 하지않기
- 중요한 명령어는 없고 다만 빈도가 높은 명령이 있을 뿐
3. vim
- 실무에서는 GUI를 못쓰는 환경이 많음
4. 실제 OS에 리눅스 설치
- 최대한 많이 사용하기(비주얼 스튜디오에서 vim plugin, chrome에 vimium)
- 실무에서 xwindow를 거의 사용하지 않기 때문에 가상 머신에서도 terminal 모드로만 사용하기
- mount 부분보다는 udiskctl 실습해보기
5. REGEX
- 중급 이상의 레벨에 올라가고 싶으면 정규 표현식 필수
- POSIX REGEX, BRE, ERE, PCRE 구분할 수 있어야 함

아래 명령어 실행에 대한 설명으로 틀린 보기를 고르시오. 
(각 행에서 $로 시작하는 행만 타이핑된 명령어이다. 따라서 아래는 총 5개의 명령행 타이핑이 있다.)

$ ls /tmp
ssh-B5DqwopEjE4W
tmux-1000
systemd-private-8050ca09d5494-systemd-resolved.service-OdwsUU

$ [ -x /usr/local/bin/clean_mxware.sh ]; echo $?
0

$ cat /usr/local/bin/clean_mxware.sh
#!/bin/bash
set +e
echo "Terminate a MX/Ware process and delete temporary files"
pkill mxware.*
sleep 10
set -e
rm /tmp/mx*.sock
echo "MX/Ware has terminated successfully"

$ /usr/local/bin/clean_mxware.sh
Terminate a MX/Ware process and delete temporary files
MX/Ware has terminated successfully

$ echo $?


a) clean_mxware.sh는 POSIX sh (본셸)로 작동시켜도 결과가 같다.
b) clean_mxware.sh는 현재 유저에게 실행 권한이 주어져있다.
c) /usr/local/bin/clean_mxware.sh를 실행하는 도중에 에러가 발생하여 중단될 것이다.
d) /usr/local/bin/clean_mxware.sh파일은 최소 10초 이상의 시간이 걸린다.
e) 마지막의 echo $?에서는 0이 출력될 것이다.
f) 답 없음 (== 틀린 보기가 없음)

답없음