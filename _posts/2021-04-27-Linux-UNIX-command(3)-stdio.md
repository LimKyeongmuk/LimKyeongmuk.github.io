---
title: "Linux Command(3) - stdio(표준 입출력)"
excerpt: ""

categories:
  - Linux
tags:
  - UNIX
  - UNIX Command
  - Linux
  - Linux Command
---
# 01. stdio(Standard Input/Output)
### file channel
  - file에 입출력하기 위한 통로
  - 파일 채널에 입출력하는데 하드웨어를 직접 접근하지않고, 표준화된 입출력 방식을 통하도록 하는 가상화 레이어의 일종
  - 하드웨어를 알지 못해도 가능
  - C언어의 I/O 인터페이스의 심플함을 가능하게 함
  - 파일에 입출력을 하기 위한 메타 정보를 가지는 객체로 프로세스 스코프에서 유효(프로세스 종료와 함께 휘발)
 
### file descripter 
  - 파일서술자, 파일 기술자, fd
  - 파일 채널들에게 붙여지는 유일한 식별자로 프로세스 내부에서만 유효
  - 양수 0번부터 시작하여 증가
  - 예약된 파일 서술자
    - 0: stdin(표준 입력)
    - 1: stdout(표준 출력)
    - 2: stderr(표준 에러)
  - fd 값은 프로세스 안에서 임의의 값을 받는 것이기 때문에 프로세스가 다르면 fd가 다를 수 있음

### PIPE
- 프로세스 사이에 통신에 사용
- IPC(Inter-Process Communication)의 일종
- shell에서 많이 사용하는 기능
- anonymous pip(익명 파이프): temporary
- named pip(명명 파이프): persistency

### anonymous pip
- 프로세스들의 직렬 연결
- 명령행에서 Vertical bar(|) 사용
- nameless pipe, unnamed pipe
- 임시로 생성되었다가 소멸되는 파이프(=익명 파이프)
- 보통 줄여서 파이프라고 하면 익명 파이프를 말하는 경우가 많음

### named pip
- UNIX에서는 구현체를 FIFO Pipe라고 부름
- 파일처럼 존재하므로 path + filename이 있음
- mkfifo 명령을 사용하여 생성

### redirection(방향 재지정)
- 채널의 방향을 다른 곳으로 연결
- A > B: A의 stdout을 파일 B로 연결
- A < B: A의 stdin을 파일 B로 연결
- A >> B: 방향은 >과 같고, 추가(append)하는 모드

### cat
- stdout와 파일을 자유롭게 연결해주는 기본 필터
- 파일의 내용을 stdout으로 출력하는 용도
- stdin의 입력을 redirection해서 파일로 출력하는 용도
