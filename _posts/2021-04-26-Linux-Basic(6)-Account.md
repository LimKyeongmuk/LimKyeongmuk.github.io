---
title: "UNIX account"
excerpt: ""

categories:
  - Linux
tags:
  - Linux
  - UNIX
  - VMWare
---
# 01. UNIX account(계정)
### UNIX account
- system account
  - root 및 system service 전용 계정
  - system service 전용 계정은 로그인 불가 계정도 많음
- normal account
  - 나중에 추가하는 로그인이 가능한 일반 유저
- 계정의 계층을 나누는 이유
  - 권한을 분리하고, 접근할 수 있는 디렉터리와 파일, 프로세스를 구분

# 02. 계정 및 그룹의 생성, 변경, 삭제
### user, group 관리 명령어
- user
  - **useradd**: user 추가
  - **usermod**: user 권한 속성 변경
  - userdel: user 삭제
  - id: user 정보
  - whoami
- group
  - groupadd
  - groupmod
  - groupdel

# 03. User 교체
### user 교체 권한 등의 명령어
- user 관련:
  - **su**: (substitute user)유저 교체
  - **passwd**: password 설정
- group 관련:
  - newgrp: (log in to a new group)새로운 그룹 변경
  - gpasswd: (group password)pw 지정

# Practice1
새로운 유저 linuxser 추가하기
> &#35; useradd -m linuxer
- -m option: user의 home dirc를 만들어주는 기능

linuxser의 암호 설정
> &#35; passwd linuxer

linuxer란 유저의 정보 확인
> &#35; id linuxer
- user id는 숫자로 관리

root user의 암호 설정
> &#38; sudo passwd

team dev2라는 그룹 가입 및 그룹 패스워드 설정, 그룹 로그인
> &#35; groupadd teamdev2
> &#35; gpasswd teamdev2

linux user에 그룹 추가
> &#35; usermod -aG teamdev2 linuxer
- a: append의 약자
- G: Group의 약자
  
linuxer id 확인해보기
> &#35; id linuxer

linuxer user로 로그인 한 뒤, 새로운 그룹으로 로그인 하기
> &#36; newgrp teamdev2

본인(linuxer) id 확인해보기
> &#35; id linuxer

linuxer에 sudo 그룹 추가(sudo를 기본으로 사용 가능한 그룹)
> &#35; usermod -aG sudo linuxer

linuxer에 추가 그룹을 모두 제거할 때
> usermod -G '' linuxer (group 모두 제거)

### su(substitute user)
- su [-] [ID]
- id를 생략 시 root 유저
- 지정시 login shell로 작동함
- 높은 권한에서 낮은 권한의 유저로 교체할 때는 패스워드 검증을 하지 않음

# Practice 2
> &#36; whoami
> &#36; su
> &#35; whoami

> &#36; whoami
> &#36; su -
> &#35; whoami
- su를 그냥 사용하면 권한, 환경설정에 문제가 생길 수 있으므로 항상 su - 로 login shell로 교체하거나 sudo를 이용하는 방식을 사용하는 것이 좋음

- login 시 셋팅되는 주요 환경변수 리스트

  |환경변수|설명|
  |:---|:---|
  |HOME|홈 디렉터리 위치|
  |HOSTNAME|현재 호스트 컴퓨터의 이름|
  |TERM|현재 사용하는 터미널의 종류|
  |HISTSIZE|명령어 히스토리 크기|
  |USER|현재 로그인 유저명|
  |MAIL|Email을 읽어올 위치|
  |PATH|실행 파일 검색 경로 리스트|
  |PWD|현재 작업 디렉터리 위치|
  |LANG|쉘의 로케일 정보, 캐릭터 세트|
  |PS1|프롬포트 형태|
  |SHELL|현재 사용하는 쉘|

# 04. man page와 locale
### man page
  - UNIX 계열은 CLI로 작동하는 online manual이 있음
  - 이를 man page라고 함
  - googling하기 전에 man page를 먼저 보자(google 검색 결과가 오래 전 작성 글일 수 있기 때문)
  - 표준 UNIX 명령어는 XPG로 유래

### man page 사용법
- man [option] &#60;item&#62;
  - ex) man useradd, man -f printf
- 문법 표기
  - 무괄호, <>: 필수(required) 항목
  - []: 선택가능(optional)한 항목

### man page: 출력 언어
- 언어 설정에 따라서 다른 언어로 출력될 수 있음
- 설치시에 한국어 설치를 했으면 대부분 한국어 설정
- 한국어 매뉴얼이 존재하는 경우, 한국어 매뉴얼 출력
- 한국어 매뉴얼이 존재하지 않는 경우, 영어 매뉴얼이 출력
- 한국어 매뉴얼은 되부분 보지 않는 편이 좋음(과거에 번역)

# Practice: man page
> &#36; man su
- &#60;Ctrl-alt-F3&#62;
> &#36; man su

### lang 환경 변수
- locale + character set
- locale: 사용자의 언어 및 지역 정보
  - language: ISO 639-1
  - country: ISO 3166-1
  - ex) ko_KR.UTF8
    - locate: ko_KR
      - language: ko
      - country: KR
    - character set: UTF-8
- language 환경변수: language의 우선순위 결정

### locale 설정 방법
- LANG 환경 변수를 먼저 설정하고 LANGUAGE를 사용하고자 하는 경우에는 우선순위의 마지막에 en을 사용하거나 배우는 것이 좋음
- locale -a: 시스템에서 가능한 로케일 리스트를 모두 출력
- 최근에는 localectl로 통합됨

### locale: C
- C언어가 작동하기 위한 최소한의 로케일
- export LANG=C → 미국이 사용하는 영문(Latin-1)을 말함

### LANG 환경 변수
- LANG 변수의 값(*#36;)을 출력(echo)
> echo &#36;LANG

### locale-gen
- 로케일이 없는 경우 로케일을 생성
- locale -a에서 ja_JP.utf8이 없는경우 ja_JP.utf8 로케일 생성
> &#35; locale-gen ja-JP.utf8

### localectl
- system locale을 확인 및 설정(설정은 root 권한 필요)
- localectl -h
- localectl &#60;double tab&#62;으로 사용가능한 명령 확인 가능
- localectl set-locale LANG=en_US.utf8

###character set(encoding): putty
- Unicode를 지원하는 터미널 SW
- putty, MobaXterm

창> 변환> UTF-8 확인하기