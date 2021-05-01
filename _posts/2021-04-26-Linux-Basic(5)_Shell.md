---
title: "Linux Shell과 환경"
excerpt: ""

categories:
  - Linux
tags:
  - Linux
  - UNIX
  - VMware
---
# 01. Linux 운영체제와 shell 인터페이스
### 운영체제(OS)란?
- Modern Operating system의 구성
  - kernel: 하드웨어를 제어하는 핵심 코드
  - application
    - shell: 다른 application을 구동시킴(커널과 application을 연결)
    - web browser, music player ...
- 실행 영역에 따른 구분
  - system(kernel)
  - user(kernel의 도움이 필요 없는 영역)
  - 모든 프로세스는 user + system으로 작동됨(하드웨어 기능 처리는 system call 담당)

### shell, User Interface
- kernel에서 볼 때 외부에 위치하여 shell
- application을 실행하는 일을 함
- Linux는 기본적으로 **bash**라는 shell을 사용

# 02. CLI, CUI, GUI의 차이
### UI(User Interface)
- 시스템의 조작 및 통신을 위해 외부의 접속을 받아들이는 부분
  - CLI/CUI, TUI, GUI가 존재
- shell은 기본적으로 CLI/CUI 방식 사용
  - CLI(Command Line Interface)
  - CUI(Command User Interface)
  - GUI(Graphic User Interface)
  
### Command Line Interface
- Prompt에서 명령행 타이핑을 통해 실행되는 구조
- 텍스트 방식
- 장점: 오버헤드가 적음(성능에서 유리), 입출력 결과를 파일로 저장 가능
- 단점: 사용자 친화적이지 않음(익숙해지면 오히려 친화적)

### Text-based (graphical) User Interface
- 텍스트를 이용해서 구현하는 그래픽을 사용하는 UI
- 장점: 오버헤드가 적음
- 단점: 화면 크기가 정해져 있고, 정해진 라이브러리를 사용해서 구현해야 함
- 주로 ncures, newt 라이브러리로 구현(python 랩퍼로 주로 개발)

### Graphical User Interface
- VGA 그래픽 방식을 사용하는 UI
- Window Manager에 의해 위젯 표현을 변경할 수 있음
- 장점: 섬세한 그래픽과 색상을 사용할 수 있음
- 단점: 높은 통신 대역폭, 메모리를 필요하므로 높은 하드웨어 성능 요구

### CLI vs GUI: performance
- UNIX, Linux를 선호하는 이유
  - Performance가 좋기 때문
  - 낮은 대역폭에서도 잘 작동되기 때문

### CLI on Linux
- CLI 명령어를 잘 다루는 사람이 유능한 인재
- CLI 명령어는 많이 사용해야 익숙

# 03. Linux 사용법: login, logout
### Console
- 사전적 의미: 건반과 패달을 가진 연주대
- 컴퓨터에 물리적으로 직접 연결된 제어용 송수신 장치를 의미

### terminal
- 사전적 의미: 종단, 끝
- 데이터를 송수신하는 목적지나 출발점이 되는 장치

### 용어의 정의
- 콘솔은 장치의 **형태**를 의미, 터미널은 장치의 **위치**에 따른 논리적 분류 용어
- console terminal, remote terminal
- 물리적인 Server가 있을 때 직접 키보드와 화면을 통해서 제어(Console)
- 보안적인 작업을 진행할 때 Network를 끊고 Console에서만 작업 진행
- 네트워크를 통해서 PC나 Notebook이 Sever를 접속할 때 Remote terminal

### console terminal
- 시스템에 물리적으로 부착되어있는 콘솔에서 액세스한 터미널
- terminal 부분을 생략하고 그냥 'console'로 불림

### remote terminal
- 원격에서 액세스한 터미널: ssh, web shell, X terminal 등등으로 접속
- 보통 remote를 생략하고 **"terminal"**로 부르는 경우가 많음
- 생략한 경우에는 console에서 해도 무방한 경우를 의미함
- 학술적으로 접근하면 웹 브라우저도 terminal software에 속함

### Console: interface에 따른 분류
- Text console vs Graphical console

### Text Console
- 대부분의 UNIX, Linux 서버가 text console 사용
- Text console 스위칭 방법
- 일반적으로 &#60;ALT-F1&#62; ~ &#60;ALT-F6&#62;에 매핑되어 있으며 tty라고 부름
- Graphical console에서 Text console 이동방법 &#60;CTRL-ALT-F1&#62; ~ &#60;CTRL-ALT-F6&#62;

### Graphical Console
- 주로 데스크탑 용도에서 사용
- X Window를 사용하는 콘솔이므로 일반적으로 X console이라고 부름
- Graphical console은 F1 or F7에 맵핑

### Login:definition
- 접속을 요청하고
- 알맞은 인증과정을 거쳐서
- 시스템 내부와 통신 가능한 상태가 되어
- 시스템에 명령을 내릴 수 있는 인터페이스가 실행되는 것(이 과정에서 초기화가 이루어지고 session이 만들어짐)

### Login:console
1. Text console
- 시스템에서 &#60;Ctrl&#62;+&#60;ALT&#62;+&#60;F3&#62; (tty3: 3번 콘솔)
- 입력을 기다리는 부분을 command prompt라고 함
2. Graphical console
- DE*(Desktop environment)에 따라서 다름
  - Ubuntu: GNOME
  - Debian, RH 계열: GNOME(default)/KDE/Xfce/MATE
- 기본 WM는 다르지만 추가 설치로 얼마든지 변경할 수 있음

### Prompt prefix
- prompt 앞에 $이면 일반 유저
- prompt 앞에 #이면 root 유저

### logout: session이 만들어지는데 이를 해제하는 것
- exit, logout, &#60;Ctrl+D&#62;

### poweroff / reboot
- 예전 Linux에서는 shutdown -h 혹은 halt == poweroff
- 최근에는 systemd 기반
  - 소프트웨어적인 종료: shutdown -h, halt
  - 하드웨어 전원을 끄는 경우: poweroff

### reboot
- 재부팅