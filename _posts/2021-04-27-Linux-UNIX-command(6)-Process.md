---
title: "Linux Command(5) - Process"
excerpt: ""

categories:
  - Linux
tags:
  - UNIX
  - UNIX Command
  - Linux
  - Linux Command
---
# 01. Process
### ps: process status
- ps는 기본적으로 현재 세션의 프로세스들을 보여줌
- PID: 프로세스 ID
- TTY: terminal ID
- TIME: CPU 시간(누적 시간) = CPU를 점유했던 시간의 누적값(현실 세계의 시간은 ETIME)
- CMD: command= 프로세스 이름(argv[0]을 의미)

### ps: all processes
- e: 전체 프로세스 보여주기(그냥 ps는 내 session의 프로세스만 보여주기)
- a: session leader와 process가 터미널에 연결되지 않은 경우를 제외하고 보여줌(실제 작동한 process만 보여줄 때)
- f: (full-format)
  - UID: 해당 프로세스의 소유권자 ID(원래 UID는 숫자, 심볼인 경우 Username)
  - PID: 프로세스 ID
  - PPID: 부모 프로세스 ID(Parent PID)
  - C: CPU 사용량
  - STIME: 프로세스를 시작한 시간(시:분)
  - TTY: terminal ID
  - TIME: CPU 시간(누적 시간) =CPU를 점유했던 시간의 누적값

### ps -l: long-format
- F: 프로세스 플래그
- S: 상태 코드(state code)(S는 동작멈춤, R은 동작중)
- PRI: 실시간 우선순위(realtime priority)
- NI: 나이스 우선 순위(nice)(-20~19로 기본값 0)
- SZ: 사용되는 프로세스 코어 이미지의 메모리 크기(KB)
- C: CPU Usages(CPU 사용량 누적된 수치 퍼센트)

### all, long, full
- ps -el: all, long
- ps -ef: all, full
- ps -ej: all, jobs(pid, pgid,sid,tty)
- ps -eo를 더 많이 사용함

### BSD, SysV style
- ps axf: BSD style
- ps -e --forest: UNIX SysV style
- 진짜 고급자들은 **alias**를 통해서 ps를 커스텀해서 사용

### Practice: ps
- e: all
- f: full
- grep bash: bash 라는 단어가 들어간 행만 뽑기
- -bash: login shell / bash: non-login bash
> &#36; ps -ef | grep bash

# 02. Process Control#1
### kill
- 프로세스를 죽이는 명령이 아니라 프로세스에 시그널을 전송(send)하는 기능
- 사용 가능한 signal 종류는 kill -l 명령으로 확인 가능

### kill: UNIX signals
- **SIGHUP**: Hang Up(연결이 끊겼을 때 발생)
- **SIGINT**: Interrupt <Ctrl-C>
- SIGOUIT: Quit <Ctrl-\> (코어를 덤프해줌)
- SIGKILL: kill(강제로 프로그램 죽임(최후의 수단))
- SIGSEGV: Segment violation(프로그램의 이상 작동으로 메모리 침범으로 죽은 경우)
- **SIGTERM**: Terminate(프로그램을 죽여달라고 요청(default))
- **SIGTSTP**: Temporary Stop(잠시 멈추기) <Ctrl-Z>

### Practice: ps, kill
- 터미널 2개 실행하기
- 1번 터미널에서 ~/.bashrc를 page 단으로 보여주기
> &#36; less ~/.bashrc

- 2번 터미널에서 ps 명령 실행 후 kill로 죽이기(SIGTERM)
> &#36; ps -e | grep less
>
> &#36; kill 4800819

# 03. Process Control
### Job Control
- fore-ground process: 현재 session에서 제어 터미널(controlling terminal)을 가진 프로세스
- back-ground process: 현재 session에서 제어 터미널(controlling terminal)을 잃어버린 프로세스
- Ctrl-Z: SIGTSTP 시그널을 fore-ground 프로세스에서 전달받으면 잠시 정지시킴(결과적으로 back-ground에 Stopped 상태로 전환)

### Session
- 세션은 멀티 유저 시스템에서 통신 객체(seat of remote)를 구별하기 위함
- 터미널에서 작업할 수 있는 공간을 받은 것이 세션을 받은 것
- 세션을 구분하기 위해서 SID 부여, 그 세션의 PID값을 SID로 부여
- SID == PID가 같은 프로세스를 Session Leader라고 부름
- 세션 ID에는 여러가지 프로세스 그룹을 만들 수 있는데 PID와 Process GroupID가 같은 것이 Process Group leader
- session은 controlling terminal의 소유가 가능함

### 제어 터미널(controlling terminal)
- 사용자의 제어를 받는 터미널 장치
- CUI에서 멀티 태스킹을 위한 제어 방법
- 제어 터미널을 소유한 프로세스는 키보드 입력을 가지고 이러한 프로세스를 fore-ground process라고 부름
- 하나의 session에서는 for-ground process를 최대 1개 가질 수 있음

### 제어 터미널의 규격
- pts/#: UNIX98 Pseudo terminal system(SUSv2, 1998)
- tty#: console terminal <Ctrl+Alt+F3 -> tty3>
- Session에서 제어 터미널을 가지지 않는 경우는 ps의 TTY 필드에 ?로 나타남

### Process Group
- 세션이 만들어지고 그 세션 안에서 명령을 내리는 프로세스의 계층
- Process group leader(=ProcessGroup ID =PID)
- PID가 음수 값이면 프로세스 그룹 전체에 시그널을 전파

### daemon
- 고아 프로세스(그리고 세션 리더)
- stdio를 모두 /dev/null로 리다이렉션
- control terminal을 가지지 않는 프로세스

### interrupt key
- CTRL-C: SIGINT
- CTRL-\: SIGQUIT

### commands
- jobs: 작업 지시를 확인하는 명령어
- fg %#: 지정된 프로세스를 back-ground에서 fore-ground로
- bg %#: 정지된 백그라운드 프로세스를 back-ground에서 running 상태로 변경 
- command &: back-ground에서 running 상태로 실행시킴

### Practice: job control
- vi 실행한 후, &#60;Ctrl-Z&#62;로 종료시키기(&#91;1&#93;&#43; Stopped가 나옴)
> &#36; vi

- vi hello.c을 실행한 후, &#60;Ctrl-Z&#62;로 종료시키기(&#91;2&#93;&#43; Stopped가 나옴)
> &#36; vi hello.c

- cat을 이용해서 명령 타이핑(마지막에 &#60;Ctrl-D&#62;로 종료)
> $ cat > printdate.sh
> &#35;&#33; /bin/bash
> while &#58; &#59;
> do
>   data &#38;&#38; sleep 1
> done
> &#60;Ctrl-D&#62;

- bash로 printdate를 실행한 후, &#60;Ctrl-Z&#62;로 종료시키기(&#91;3&#93;&#43; Stopped가 나옴)
> &#36; bash printdate.sh

- jobs로 작업 지시 확인하기
> &#36; jobs

- 2번 작업을 포그라운드로 불러오고,  &#60;Ctrl-Z&#62;로 종료시키기
> &#36; fg %2

- jobs로 작업 지시 확인하기(가장 최근 실행 프로그램은 +, 그 바로 이전은 -)
> &#36; jobs

- 3번 작업을 background에서 running 상태로 변경시키기( &#60;Ctrl-Z&#62;로 종료시켜도 종료되지 않음 → 제어 터미널을 잃어버렸기 때문)
  - foreground로 불러와서 죽이거나 kill 명령으로 죽여야함
> &#36; bg %3
> &#60;Ctrl-D&#62;