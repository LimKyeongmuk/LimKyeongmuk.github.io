---
title: "리눅스 bash 기초"
excerpt: ""

categories:
  - Linux
tags:
  - Linux
  - UNIX
  - bash
  - bash shell
---
# 학습 목표
1. bash 문법 및 스크립트 작성 방법 학습
  - bash 구동에 영향을 끼치는 POSIX 시스템의 fork-exec 개념 학습
  - fork-exec 과정에서 shell의 exit code와 관련 부분 학습
  - bash 옵션과 systemd 구조의 Linux에서 영향을 주는 logind 설정 확인
2. bash와 stdio 표준입출력의 관계
3. bash script를 작성하는데 유용한 외부 UNIX 명령어 활용법
4. shell과 유용하게 사용되는 REGEX 표준 정규표현식
  - POSIX BRE, ERE와 둘을 구분하는 문법 규칙
  
# 01. bash shell 배경
### Shell(쉘)이란
- Application을 실행하기 위한 도구
  - Hardware를 제어하기 위해서 Kernel 존재
  - Kernel은 일반 유저 접근 불가능
  - Application을 Kernel과 실제 통신을 위하면 중간 매개체인 Shell 필요
- 실질적으로 커널은 Shell에서 내린 명령어의 Process를 관리하는 역할

### Shell의 역사
- Bourne shell: /bin/sh
  - Stephen R. Bourne이 작성(1977)
  - IEEE std 1003.1 표준에 채택되어 **POSIX shell**로 명명(**UNIX 표준**)
  - 유닉스는 기본적으로 파직스 표준에 있기 때문에 본쉘을 탑재해야 함
  - 기능이 미약했고 대부분의 기능을 외부 명령어로 대체해서 사용했기 때문에 지금은 많이 사용하지 않음
  
- C shell: bin/csh
  - Berkley의 Bill Joy가 작성(=BSD UNIX에 포팅)
  - C 언어와 비슷한 문법으로 인해 C shell 명명
  - 기존 Bourne shell과 너무 다른 문법 때문에 호환성 문제로 외면 받음
  
- Korn shell: /bin/ksh
  - Bell Lab.의 David Korn이 작성(1983)
  - **UNIX의 sh 개량형**(실질적으로 가장 많이 사용)
  - csh, bourne shell의 기본 기능 모두 포함(하위 호환성)
  - **vi prompt mode** 지원
  - 현대적인 상용 유닉스의 기본셸
  
- bash: /bin/bash
  - Born again sh
  - 표준은 아니지만 GNU에서 발전시킨 Bourne shell의 개량판(1989)
  - Korn shell이 라이선스 문제로 계속 사용하지 못해서 벤치마킹
  - 현대적인 shell의 기능 대부분이 추가되어 Korn shell과 호환됨
  - 많은 부분이 built-in cmd로 대체되서 fork-exec 횟수가 감소하고 성능이 향상함
  - 대부분의 유닉스에서도 많이 사용되어짐
  - Linux, Mac OSX에서 가장 많이 사용되는 shell
  
- dash
  - Small and fast POSIX-compiant shell (Ubuntu에서 사용)
  - Bash보다 가볍고 빠른 shell을 목표로 만들 shell
  - 현실에서는 사용하지 않는게 좋음
  
- Zsh: /bin/zsh
  - Paul Falstad가 작성(1990)
  - 커스터마이징이 다양함(Oh My ZSH! 사용)
  - 서버 측에 문제가 많아서 자주 사용하지 않음
  - 데스크탑 편의성에 맞춘 기능이 많음
  
### 특수문자 읽는 법
  - &#96;: backtick
  - &#126; tilde
  - &#35;: hash
  - &#94;: caret
  - &#92;: bask slash(leader key, escape)

# 02. bash command prompt(입력기 모드 emacs vs vi)
### input mode
- typing 치는 것이 system 전체에 큰 text file에서 typing 친다고 생각해서 Enter 동시에 다른 명령을 보여주는 개념
- typing친 text file은 history에 저장
- 이 문서를 편집하는 편집기를 결정하는 것이 입력기(input mode)
- input mode에는 2가지 방식 입력기 지원
  - bash 기본 모드: emacs mode(default) → set -o emacs
  - ksh과 호환을 위해서 사용: vi mode → set -o vi
  
- set -o: human readable(사람이 읽기 편한 방식으로 보여주기)
- set +o: machine readable(머신이 읽기 편한 방식으로 보여주기)

### emacs editing mode key
- &#94;A, &#94;E: 라인 앞으로 이동, 라인 뒤로 이동
- &#94;L: 클리어 (현재 라인을 모두 스크롤 업, clear는 예전 명령)
- &#94;P, &#94;N: 이전 명령어, 다음 명령어(화살표 기능도 가능, history 명령으로 목록 확인 가능)
- Tab: 명령어 자동 완성(Command completion)
- &#94;R, &#94;S: 명령어 후방, 전방 탐색(계속 누르면 재탐색, &#94;S는 설정이 필요할 수 있음)
  - 기본적으로 Linux bash에서는 &#94;S를 STOP으로 사용하고 있음
  - ~/.bashrc에 stty STOP '' 명령 넣기
- Tab Tab: prefix와 매칭되는 파일 탐색 및 완성
- &#94;W: 커서 위치에서 앞으로 한 단어 잘라내기, 뒤로 한 단어 잘라내기

### vi edting mode key
- ESC키를 누르면 normal mode 사용 가능
- vi와 문법 동일

### recall command line key
- ALT-.: 이전 명령어의 마지막 argument를 다시 불러올 수 있음
- !:n-m: 이전 명령어의 n~m th argument list. range 지정시 사용

### 연습 문제
화면에 "Hello UNIX" 찍기
> &#36; echo "Hello UNIX"

루트 위치부터 lib로 시작하는 파일명 모두 찾기
> &#36; find / -name "lib*"

"Gnu is Not UNIX" 문자열 찍기
> &#36; echo "Gnu is Not UNIX"

pid, cpu 사용량, command 찍어주기
> &#36; ps -eo 'pid, cp, cmd'

"Hello POSIX" 문자열 찍기
> &#36; echo "Hello POSIX"

홈 디렉터리에서 일반 파일 검색
> &#36; find ~ -type f

ps 명령
> &#36; ps aux

&#94;R을 눌러서(search한다는 명령) U까지만 쳐도 echo "Gnu is Not UNIX"가 나옴

&#94;R&#94;R 누르면 echo "Hello UNIX"가 나옴

&#94;R을 누르고 fi를 누르면 find ~ type f가 나옴

&#94;R&#94;R 누르면 find / -name "lib*"가 나옴

Enter를 누르면 검색된 명령어 바로 실행하고 Tab키를 누르면 Copy 해줌

# 03. bash shell의 작동 원리
### sub-shell
- shell에서 실행되는 UNIX 명령어는 sub-shell이 생성되어 실행
- sub-shell이란 fork-exec로 만들어지는 child process shell
- 외부 명령어 실행은 기본적으로 서브셸을 생성하고, 그 서브셸이 다시 원래 실행되려는 process를 교체하려는 과정
- 만약 ls -al 명령을 하면 bash fork 과정을 통해서 자식 프로세스를 만듬
- fork를 통해서 bash가 하나 더 만들어지고(sub shell 생성)
- 이미지를 교체하는 exec 명령을 실행하고 ls -al 명령이 교체되서 실행한 후 다시 부모 프로세스한테 실행이 종료되었다는 exit code(return code) 반환
- 자식 프로세스의 성공 여부를 통해 다음 작업을 진행 및 정지하기 위해서 exit code를 받아야 함
- exit code: 0(true) or non-zero(false)
- sub shell에게 일을 시킨다는 것은 하나의 함수를 호출하는 것과 비슷
- child process를 만들어서 exit code를 받고, 성공/실패에 따라서 분기하는 행위가 중요
- fork는 기본적으로 부모 프로세스의 자원을 복제하는데, 이전 자원을 해제하고 새로운 자원으로 교체하는 exec를 곧바로 발생하면 새로운 프로세스 이미지를 로딩하면서 바로 이전에 fork에서 복제한 parent process의 old image 자원이 곧바로 해제
- 따라서 fork-exec에서는 곧바로 해제할 쓸모없는 메모리 복제를 피하기 위해서, fork 시 메모리 복사를 pending하는 Cow(Copy on Write) 기법이 나오게 됨
- 요즘은 FS에도 CoW 기능이 들어가는 경우도 있음

### zombie
- Parent process가 종료되면 child process의 exit code를 받는게 정상
- child process가 exit code 보냈는데 Parent process가 받지 않는다면 그 상태를 zombie process or defunct process라고 부름
- 모든 프로세스의 종료는 zombie 상태를 거쳐서 해소되고 zombie 프로세스는 프로세스가 종료되는 정상적인 과정 중 하나
- child process 프로세스의 Meta 정보가 남아있는 것으로 메모리에는 큰 용량을 차지하지 않음
- zombie process가 생긴다는 것은 zombie process의 parent process가 문제가 있다는 것

### zombie example
- 시스템에서 child process의 exit code를 이용해서 성공, 실패를 확인하는 예시
1. Hello world(C언어 작성 코드) 예제를 빌드하여 실험

2. make 빌드 툴이 sub-make를 실행하여 처리할 때 exit code를 처리하는 방법보기
touch는 뒤에 있는 파일이 있으면 시간 새로 갱신, 없으면 0byte짜리 빈 파일 만들어주기
> &#36; touch qwer

qwer 삭제
> &#36; rm qwer

바로 이전 명령(rm qwer)의 결과값
> &#36; echo &#36;?
> 
> 0

한 번 더 삭제(더이상 지울 qwer 파일이 없기 때문에 false)
> rm qwer
> 
> &#36; echo &#36;?
> 
> 1

- rm --force 옵션에 대한 오해
  - rm은 파일을 지우는 명령
  - rm으로 파일을 지우는데 파일이 없는 경우도 있음
  - 무조건 return 값을 무시할 때 사용하는 것이 force 명령
  - Makefile에서도 많이 사용됨(rm 명령어의 결과를 무시해야 하는 경우에 -rm으로 많이 사용)
  
3. systemctl 명령어로 시스템에서 작동하는 각정 service들이 exit code를 통해 성공, 실패를 판별하는 것

### exit code 정리
- UNIX-like 시스템에서 프로세스의 return 코드가 exit code
- 이 값은 parent process에 보고가 되고, parent process는 자식 프로세스가 어떻게 종료되었는지 알 수 있음
- shell script를 작성한다는 것은 모든 명령어 체계들이 exit code를 보고, 계속 프로그램을 실행할 것인지, 종료할 것인지, 에러를 보고할 것인지 결정
- A, B, C 순서로 배치를 돌아야 하는데, B가 실패했음에도 불구하고 C가 작동되면 큰일날 수 있음
- shell script도 정삭 작동이 아닌 경우 return 코드를 non-zero로 주도록(exit 1) 작성해야함

### shell script
- 셸의 명령 기능을 조합하여 batch 처리를 목표로 함
- 외부 명령어(external command)와 built-in 명령어를 조합할 수 있음
- 셸 스크립트로 해결되지 않는다면 perl, **python**, ruby와 같은 스크립트 언어를 사용할 수 있음

# 04. Variables
### 변수
- 선언하는 방법(declaration)
  - [set] name=value: name이라는 변수에 value를 선언 or 변경
  - = 사이에 공백은 허용되지 않음
  - 기본으로 모든 데이터는 문자열 타입으로 저장

- 가져오는 방법(expression)
  - ${name}
  - 중괄호 생략 가능(앞 뒤로 공백이 있는 경우)

- 삭제하는 방법(removal)
  - unset <name>
  - name에 해당하는 변수를 삭제
  - 일반적으로 많이 사용하지는 않음

-  공백(space)
  - 셸 프로그래밍에서는 공백 자체가 특별한 역할
  - 임의대로 생략하거나 넣으면 오류
  - 변수를 넣을 때 띄어쓰기를 사용하면 변수명을 명령어로 생각함

### 환경 변수(environment variable)
- 환경변수 특징
  - 셸 변수에 상속 속성을 설정하는 변수
  - fork 하는 경우 child process에 상속됨
  - child process가 shell이라면 변수는 읽는 방법은 그대로 읽을 수 있음
  - C언어 프로그램에서 getenv()로 환경 변수를 읽을 수 있음

- 환경변수 생성(declaration)
  - declare -x나 export를 사용(아래 3가지는 동일한 명령)
  > MY_ENV_VAR="ultramuk"; export MY_ENV_VAR
  > 
  > **export MY_ENV_VAR="ultramuk"**
  > 
  > declare -x MY_ENV_VAR="ultramuk"
  
  - 환경 변수는 관습적으로 대문자 사용
  
- built-in 환경 변수
  - TERM: 사용하는 터미널 종류
  - LANG: 로케일 정보(명령어 출력 언어 및 문자세트 지정)
  - EPOCHSECONDS, EPOCHREALTIME: UNIX epoch time(bash5에서 지원, 환경변수 X)
  
- 터미널 환경변수의 확인 및 설정
  - 터미널 환경 변수 확인
  > &#36; echo &#36;TERM;
  
  - 터미널 환경 변수 설정
  > &#36; export TERM=xterm-256color

  - 모든 환경 변수 보기
  > env
  
- 로케일 환경변수의 확인 및 설정
  - 로케일 환경변수 확인
  > &#36; echo &#36;{LANG}

  - 로케일 환경 변수 설정
  > &#36; export EDITOR=vim
  
- EDITOR 환경 변수
  > &#36; export EDITOR=vim
  
  - CTRL-X CTRL-E를 입력하여 명령행을 에디터에서 사용
  - 긴 명령어를 에디터에서 입력할 때 사용
  - 터미널 명령행이 이상해서 긴 명령어 타이핑이 힘들 때 꼭 사용하기
  
- 명령어 탐색 기능
  - PATH
  - PATH 추가를 위해 pathmunge 셸 함수 사용
  
- shell 변수와 환경 변수
  - shell 변수는 기본적으로 라이프사이클이 process와 같음
  - 프로세스가 종료되면 shell 변수의 메모리 공간도 해제
  - permanency를 제공하기 위해서는 rc(run command, runtime config) 파일(bash가 실행될 때마다 매번 파일을 읽어들이는 것)에 등록
  - .bashrc, .bash_profile에 넣어줌
  
# 05. Shell의 실행타입(Invoking type)
### Authentication 과정에 따른 분류
- login shell
  - login할 때 실행되는 셸의 타입
  - login: 알맞은 인증과정을 거쳐서 시스템 안에 들어오는 것
  1) /etc/profile 해당 파일이 존재한다면 import 
    - 해당 파일은 global profile로 모든 환경에 영향을 줄 수 있음(수정하면 위험함), 로그인 환경 설정
  2) ~/.bash_profile → ~/.bash_login → ~/.profile 순으로 읽음(앞에 .이 붙었으므로 hidden file)
    - 각각 shell이 달랐기 때문에 모두 호환 모드로 작동시키기 위해서 위와 같은 방법으로 읽음
    - RH 계열: .bash_profile / Debian 계열: .profile
  3) 종료시 ~/.bash_logout이 존재하면 실행
  
  - 위는 일반적인 경우이며 bash 버전 및 커스텀 설정에 따라서 조금 다를 수 있음
  - bash가 sh로 실행하면 POSIX standard mode로 실행되며 /etc/profile → ~/.profile 순으로 실행
  
- non-login shell
  - 로그인 하지 않은 셸은 X window에서 터미널을 실행했을 경우
  1) import ~/.bashrc(if exist)
    - login shell에도 .bashrc를 실행함(항상 실행됨)
  - "su -": login / "su": non-login
  
~/.bashrc에서 .bash_aliases를 import
> alias enus="export LANG=en_US.UTF-8; export LANGUAGE="
> 
> alias kokr="export LANG=ko_KR.UTF-8; export LANGUACE=en:ko"
> 
> alias vi=vim
> 
> alias man="LANG=en_US.utf8 man"

- import, source
  - .: bourne shell
  - source: bash 명령어
  - 외부 설정을 import 할 때 환경 변수나 셸 스크립트를 현재 셸에서 실행
  - shell을 다시 실행하기 귀찮기 때문에 다음과 같이 사용
  > . ~/.bashrc

### Command-execution 방식에 따른 분류
- interative shell
  - shell과 상호 대화형으로 대화하듯이 작동
  - source 혹은 . 명령을 이용하는 방식이 바로 현재 셸에서 import하는 방식
  
- bash Special parameter
  - h: hashall
  - i: ignoreof
  - m: monitor
  - B: braceexpand
  - H: histexpand
  
- non-interactive shell
  - 순차적으로 실행되는 script mode(프로그래밍 실행 모드)
  - sub-shell을 생성하여 스크립트를 처리하는 경우
  - 추가적으로 -e 옵션을 줄 수 있음(실행할 때 옵션을 켜주는 방식)
  - 코드에서 set -o로 옵션 지정 가능

### Summary
- interactive vs non-interactive
  - scirpt 실행은 non-interactive 모드에서 작성

- .bashrc와 .bash_profile은 어떤 차이?
  - login shell로 작동할 때는 .bash_profile을 먼저 읽고 그리고 .bash_profile이 .bashrc를 읽음
  - non-login shell로 작동할 때는 .bash_profile을 읽어들이지 않고 .bashrc만 읽음
  - 데비안 계열에서는 .bash_profile 대신에 .profile 파일을 따로 읽음
  
- alias 지정하면 어디에 넣을까?
  - Debian 계열: .bashrc를 직접 편집하거나(비추천) ~/.bash_aliases를 편집
  - RH 계열: .bashrc를 직접 편집하거나 .bashrc에서 .bash_aliases를 import하는 코드를 집어넣음
  
- man page는 영어로 읽는 경우가 많기 때문에 LANG=en_US.utf8 실행

### 실제 조합 방식(3가지)
- interactive login shell
- interactive non-login shell
- non-interactive shell(항상 non-login)

# 06. Script 구조
### scripting
- 셸에서 스크립팅하는 목적은 bash 문법을 실행할 수 있도록 작성하는 것이 길어질 경우 매번 타이핑 하기 어렵기 때문에 파일로 저장하여 반복 실행을 쉽게 할 수 있도록 하기 위한 목적
- 파일 실행 권한을 주거나 혹은 bash가 실행하면서 불러오도록 해야 함
- .bash_profile, .profile, .bashrc도 script 문법 사용

- command line
  - 커맨드 라인에서 스크립트를 타이핑하기는 어려움(오타나면 다시 쳐야 함)
  
- file
  - scirpt file 구조: 관습적으로 *.sh 파일명을 갖음
  - c shell에서는 .csh(요즘 많이 사용하지 않음)
  - 실행 셸 선언부: &#35;&#33;으로 시작(원래 &#35;는 주석)
  - &#35;&#33; /bin/bash: shell을 실행시킬 shell의 binary 위치(가장 많이 사용)
  - &#35;&#33; /usr/bin/env: binary 위치를 탐색해서 찾아내기(많이 사용하지 않음)
  - &#35;&#33; /usr/bin/env LANG=en_US.UTF-8 bash 환경변수 설정
  
  - 실행 방법
    1) sh myhome.sh(이렇게 하면 안됨)
    - sh로 작동하면 옛날 본셸로 작동함
    
    2) . myhome.sh(이렇게 하면 안됨)
    - .이나 source는 실행이 아니라 import하는 기능(자식 프로세스를 만들지 않기 때문에 실행이 이상해질 수 있음)
    
    3) bash myhome.sh
    - myhome.sh 파일을 읽어서 bash로 실행
    
    4) ./myhome.sh 
    - x 권한이 없을 때는 chmod로 x 권한 부여(chmod +x)
  
  - /bin/bash와 /bin/sh는 다름
  - /bin/sh는 symlink 혹은 hardlink
  - sh로 실행하면 POSIX shell로 작동해서 최소한의 기능만 작동하기 때문에 에러가 날 수 있음
  
# 07. Variables #2
- shell에서 만드는 변수는 기본값이 전역변수
- 지역 변수를 만들기 위해서는 변수 앞에 local이라고 적어주기
- 지역 변수는 function block에서 recursive code에서만 사용(자주 사용하지 않음)
- 변수에 문자열을 기록할 때, 공백이 있다면 항상 따옴표로 묶어줘야 함
  - "": 문자열을 표기, 특수 문자를 해석해서 표현
  - '': 문자열을 표기, 특수 문자를 그댈 표현
  - \: 문자의 특수한 기능을 제거(이스케이프)
  - ``: 묶여진 문자열은 명령어로 인식됨, 명령어의 결과값이 치환되서 보여줌, $(...)로 대체해서 사용
  - 둥근 따옴표로 인한 오류가 있음(확장된 텍스트 포맷을 제공하는 에디터에서 편집할 때 주로 발생)
  
### Practice: OS 정보 알아보기
- tr -d: 특정 문자를 delete

### Summary
- single quotes는 문자열 그 자체만을 표현
- double quotes는 문자열 안의 표현식을 해석($나 backtick을 해석)
- 해석하고 싶지 않은 것에는 이스케이프를 적어줌(\)
- single, double quotes를 섞어서 사용할 수 있음
- backslash는 meta 문자를 해석을 막는 기능을 가짐
- backtick은 $(...)를 대체하는 것을 추천

# 08. 제어 구문
### condition 분기 구문: 참, 거짓에 따라 바뀌거나 여러 선택지에 따라서 달라지는 구문
- if
  - 조건이 참일 때, 어떤 것을 실행하는지 나옴
  - if [ condition [-a|-o] condition ]; then ... fi
  - -a (AND)나 -o (OR)로 결합 가능
  - condition의 앞 뒤엔 공백이 있어야만 함
  
- if vs test(구형)
  - shell의 if 구문은 test 구문의 built-in cmd
  - test "구문" [-a|-o] "구문" [-a|-o] ...: 반환값 $?를 검사하여 0이면 true, non-zero이면 false
  - short circuit 기능: &&로 결합해서 앞에 것이 False라면 뒤의 것은 실행하지 않음
  - test $USER != "root" && echo '!root'(△)
  - [ $USER != "root" ] && echo '!root'(○)
  - /usr/bin/tet $USER != "root" && echo '!root'(X)
  
- if 안에서 사용할 수 있는 산술연산자(숫자)
  - a -eq b: equal
  - a -ne b: not equal
  - a -gt b: greater than
  - a -ge b: greater / equal
  - a -lt b: less than
  - a -le b: less equal
  
- 문자열에 대한 연산자
  - a == b(a = b)
  - a != b
  - -z X: 문자열 A의 길이가 zero이면 참
  - A -a B: AND 결합(&&로 대체 가능)
  - A -o B: OR 결합(||로 대체 가능)
  - !(A): A의 부정
  
- 파일의 상태
  - -r file: 해당 file이 존재하고 읽기 가능
  - -w file: 해당 file이 존재하고 쓰기 가능
  - -x file: 해당 file이 존재하고 실행 가능
  - -e file: 해당 file이 존재
  - -f file: 해당 file이 존재하고 일반 파일
  - -s file: 해당 file이 존재하고 용량이 zero 이상
  
- 디렉토리와 권한
  - -d dir: 해당 dir이 존재하면 true
  - -h file: 해당 file이 존재하고 symbolick link
  - -b file: 해당 file이 존재하고 block device
  - -c file: 해당 file이 존재하고 charactor device
  - -u file: 해당 file이 존재하고 SUID가 세팅
  - -g file: 해당 file이 존재하고 SGID가 세팅
  - -k file: 해당 file이 존재하고 Sticky bit가 세팅
  - -p pile: 해당 pipe가 존재
  - -S socket: 해당 socket file이 존재
  - -t FD: 해당 FD가 열려있고 터미널에 연결
  
- 파일
  - File1 -nt File2: newer-than
  - File1 -ot File2: older-than
  - File1 -ef File2: exact-file, File1과 File2가 같은 inode
  
### Practice: test statement
- 비교할 때 연산자 앞뒤로 항상 공백 확인하기(공백을 한쪽만 빼면 명령으로 인식해서 Error가 날 수 있음)
- 숫자의 크기를 판단할 때 >, < 기호로 쓰는 실수를 하면 안됨(>는 결과값이 파일로 저장하는 명령어)
- test 구문은 [ ]로도 사용 가능(공백 중요)
- 변수가 값이 할당되어 있는지 확인해보는 방법으로 -z 사용(shell에서는 변수에 값이 없어도 null로 작동, "x${}"는 옛날 방법)
- 연산자 사이에 공백 중요(공백이 없으면 assignment 연산이므로 항상 true가 나올 수 있음)

- test: lexicography
  - 문자열 비교 중에 /> or /< 기능이 있지만 자주 사용되지 않음

- test 구문의 추가적 기능을 탑재한 [[ ]] 문법
  - if [[ condition ]]; then ... fi
  - 기존의 test 구문에 추가적인 기능 탑재
  - 문자열 크기 비교 가능(escape 필요 없음, ASCII로 비교)
  - base에 따라서 숫자 비교 가능(16, 8, 그 외의 진수법 비교 가능)해서 네트워크 관련 숫자나 표기를 인식할 때 편릳
  - 파일 비교 연산 추가 기능(newer, older)

- 다중 분기
  if와 fi 사이에 elif와 else 추가
  
### Practice: control: if(RH 계열)
- 종료값에 error를 출력해주고 싶으면 non-zero로 리턴(1~255)
- &&를 많이 사용하는 것보다 if ... then ... fi로 구분해서 사용해주기(오류 방지를 위해)

- 다중 상태 분기: case
  - C 언어의 switch와 같은 기능
  - case로 시작했기 때문에 마지막은 esac
  - 값에는 value1) 와 같은 형태, default는 *), break는 ;;
  
- case 예제: pathmunge
  - $1: 첫번째 인수
  - ${PATH}: 명령을 짧게 쳐도 명령을 탐색할 수 있는 리스트
  - $1이 중간에 들어가 있으면 break, 없다면 default로 가서 $2가 "after"이면 맨 뒤, "after"가 없으면 맨 앞
  
### loop, control 구문
- while
  - 어떤 특정한 조건이 참일 때 계속 실행
  - while [ test condition ]
  - 실제 루프를 돌 구간은 do done으로 묶여있음
  - 무한 루프를 돌고 싶을 때에는 test condition에 1을 넣거나 :하나 넣어줌(null command)
  - null cmd는 0이거나 true를 의미
  - 무한 루프인 경우 break로 멈출 수 있음
  
- foreach
  - 뒤에 값들이 있고 값들이 iterator 안에 하나씩 들어오는 것
  - 특정한 단어나 숫자를 사용해야 할 때 외부 명령어인 seq로 사용(옛날 방식)
  - interactive shell에서 seq 1 10을 명령해보기 
  
- until
  - while과 반대되는 성격으로 test 구문이 False인 경우 실행
  - until [ test_condition ]
  
- select
  - 메뉴를 선택하기 위한 기능
  - shell 스크립트를 만들때 interactive로 작동할 때 사용
  - 사용빈도가 굉장히 적음
  
### Practice
1~100까지 합산하는 스크립트(while, until, for)
> sum = 0
> 
> iter = 0
> 
> while [ $iter -le 100 ];
> 
> do
> 
>   sum='expr $iter + $sum'
> 
>   iter='expr $iter + 1'
> 
> done
> 
> echo $sum

합산 스크립트를 파일로 만들어보기(sum_num.sh으로 만들었다고 가정)

- sum_num.sh 100이라고 할 때 #0: sum_num.sh, #1: 100
- 사실 expr을 사용하는 방법은 구식이지만 사용 방법은 알아놔야 함

> #! /bin/bash
> 
> if [ $# -lt 1 ]; then
>  
>   echo "Usage : $0 <number>"  
> 
>   exit 1
> 
> fi
> 
> sum = 0
> 
> iter = 0
> 
> loops = $1
> 
> while [ $iter -le 100 ];
> 
> do
> 
>   sum='expr $iter + $sum'
> 
>   iter='expr $iter + 1'
> 
> done
> 
> echo $sum

입력된 숫자가 10000을 넘으면 Argument value too big이라는 메세지 출력 후 종료

> #! /bin/bash
> 
> if [ $# -lt 1 ]; then
>  
>   echo "Usage : $0 <number>"  
> 
>   exit 1
> 
> fi
> 
> sum = 0
> 
> iter = 0
> 
> if [ $1 -gt 10000 ]; then
> 
>   echo "[ERROR] Argument value too big == $1"
> 
>   exit
> 
> fi
> 
> loops = $1
> 
> while [ $iter -le 100 ];
> 
> do
> 
>   sum='expr $iter + $sum'
> 
>   iter='expr $iter + 1'
> 
> done
> 
> echo $sum

  - 실행하기 위해서는 ./sum_num.sh 10001
  - 실행 권한을 주기 위해서는 chmod +x ./sum_num.sh
  - 실행 모드를 변경시키지 않고 실행하려면 앞에 bash 붙여줌

# 09. 확장 표현식
### 변수의 길이
- ${#name}: 변수의 길이
- ${name:offset}: 변수의 offset 위치부터 출력(0부터 시작)
- ${name:offset:len}: 변수의 offset 위치부터 len 길이만큼 출력(len이 음수일 경우에는 뒤에서부터 계산)

### 지정 가능한 변수 타입
- 2가지 지정 가능 타입 존재
  - integer(정수형)
    - 설정: <declare|typeset> -i varname
    - 해제: <declare|typeset> +i varname
    - 읽기: $varname
    - 가감: ((varname++)), ((varname--)), ((수식))
    
    - declare와 typeset은 호환 가능한 명령(typeset은 ksh에서 유래)
    - 정수형 변수에 정수가 아닌 값(일반 문자열)을 입력시 "0"으로 처리
    - practice: integer variable
      - 변수를 integer로 선언하고 문자열을 집어넣으면 0으로 처리
    
    - practice: arithmetic expansion
      - 계산식뿐 아니라 치환하는 곳에서 문자열로 값을 가져올 때는 문자열 앞에 $를 붙여서 가져옴
    
  - array(배열): 배열이라고 하지만 key=value를 가지는 dictionary에 가까움
    - indexed array: 키를 숫자로 사용
      - 설정: <declare|typeset> -a varname[=( "value" [...] )]
      - 추가: varname+=( "value" [...] )
      
    - associative array(bash 4부터 지원): 키를 문자도 지원
      - 설정: <declare|typeset> -A varname[=( [key]="value" [...] )]
      - 추가: varname+=( [key]="value" [...] )
      
    - Common
      - 읽기(value): indexed array: ${varname[0]} ${varname[1]} ... / ${varname[key]}
      - 읽기(key): ${!varname[@]}
      - 읽기(전체): ${varname[@]} or "${varname[@]}" / ${varname[*]} or "${varname[*]}"
      - 읽기(부분): ${varname[@]:0:3} / ${varname[@]:2}
      - 읽기(개수): ${#varname[@]} / ${varname[*]}
  
    - Practice: indexed array
      - 공백이 존재하는 경우에는 따옴표(홀 or 쌍)으로 감싸주기
    
    - Practice: indexed array: subset
    - Practice: indexed array: append
      - +=를 이용해서 배열을 추가할 수 있음
    
    - Practice: indexed array: unset
    - Practice: indexed array: unset, append
      - 중간의 요소를 삭제한 뒤에 추가하면 중간에 빈 index가 생기므로 foreach로 빈 곳을 찾아서 넣어야 함
      - unset보다 덮어쓰는게 더 나을 수 있음
      
    - Practice: indexed array: quotation
      - @: 기본적으로 IFS의 첫번째 문자를 구분자로 사용하게 됨(쌍따옴표로 묶어주면 각 요소를 묶어줌)
      - *: IFS의 첫 문자를 구분자로 사용해서 하나의 문자열로 출력
      
      - IFS: Internal Field Separator로 데이터 처리시 중요
        - echo -n "$IFS" | hexdump -C
          - 0x20: Space
          - 0x09: HT(Tab)
          - 0x0a: LF(new line)
        
        - 위 3개 중에 하나의 값이 들어오면 필드로 자른다는 것
      
      - IFS를 변경(comma)
      - 배열을 구분자에 영향받지 않고 처리할 때는 IFS를 null로 변경하고 명령(IFS='')하고 마지막이 IFS를 default value로 돌려놓기(다른 변수에 잠시 IFS를 저장했다가 되돌리는 방식도 있음)
  
    - Practice: associative array
  
### Summary
  array 값을 읽어올 때
  - ${...[@]}를 "..."으로 감싸면
    - 각각의 배열 요소는 개별적인 **separated strings**로 변환
    - "0번째" "1번째" "2번째" ... 식으로 분리되어 처리
  
  - ${...[*]}를 "..."으로 감싸면
    - 배열 요소를 IFS의 첫 문자를 구분자로 하여 **single string**으로 변환
    - default IFS의 첫 문자는 0x20(space)이므로 공백으로 연결된 single string이 됨
    - IFS를 comma를 사용하며, CSV 출력으로 변환할 수 있음
  
  - 가장 좋은 방법은 IFS를 null로 사용하는 것

# 10. Specical params.
- **$$**: 현재 셸 프로세스의 PID(보통은 자신의 PID이지만 $( )로 묶인 서브셸로 실행된 경우라면 치환이 먼저 이뤄지므로 서브셸이 아닌 부모 프로세스의 PID가 나옴)
- $!: 마지막으로 실행된 백그라운드 프로세스의 PID
- **$?**: 최근 포그라운드 프로세스의 종료값(zero=true, othrewise=false)
- $_: 이전 명령어의 마지막 인수(!$와 같음), history 기능으로 !:#으로 인수의 번호를 직접 지정할 수도 있음
- $-: on 되어 있는 실행 옵션 보여주기(himBH)

- **$#**: 인수의 개수(argc)
- **$0, $1, $2, ...**: 인수 리스트(argv)
- $*: 명령에 전달된 인수 전체의 목록을 IFS의 첫번째 문자를 구분자로 사용
- $@: 전달 인수를 문자열의 배열로 표시
- "$*": argv를 IFS의 첫번째 문자를 separator로 1개의 string으로 만듬
- "$@": argv를 입력한 형태 그대로 쌍따옴표로 묶어줌(공백이 오는 경우에는 일반적으로 사용)

### practice: backup script
특정 디렉토리를 현재 날짜가 들어간 파일명으로 백업
- 오류 메시지를 보지 않고 무시하려면 command 2>/dev/null (파일로 남겨주기)
- 압축을 zstd 많이 사용

# 11. Stdio
### history
- UNIX standard I/O는 C언어로부터 유래
- Dennis Ritchie는 H/W의 입출력 장치가 변경, 추가되더라도 프로세스의 입출력 부분이 영향받지 않도록 계층화 해야함을 강조
- 추성화된 입출력 계층이 필요하고 장치에 대해 투명성을 제공해야 한다고 함
- 투명성(transparency)는 추상화된 계층이 끼어있음에도 불구하고 HW에 접근하는 유저는 중간 어떤 매개체가 작동하는지 보이지 않는다는 것
- 투명성을 제공하는 시스템은 중간에서 작업을 해줘서 사용자가 편리하게 이용하게끔 하는 것
- block device(저장 장치)나 character device(입출력 장치)는 모두 abstract stream(byte base)로 설계 가능
- 유닉스 계열의 /dev/*를 오픈할 때 장치에 따라서 추상화된 번호(fd)를 부여함
- UNIX standard I/O를 어떤 HW나 하드웨어를 사용하던지 그 제품에 맞춰서 커스텀마이징할 필요가 없음

### UNIX stardard I/O
- stdin: standard input(0)
- stdout: standard output(1)
- stderr: standard error(2)

- redirection
  - stdio의 입출력 방향을 변경
  - 추상화된 장치이므로 논리적인 연결(open, connect), 해제(close, disconnect) 등 가능 
  
- redirection: file
  - &#62; FILE: 표준 출력을 파일로
  - &#62;&#62; FILE: 표준 출력을 기존의 파일에 덧붙이거나(append 방식) 없으면 새 파일로
  - &#60; FILE: 표준 입력을 파일에서 입력
  - &#60;&#60;&#60; FILE: 표준 입력에 텍스트를 입력
  - : &#60;: null command를 이용하면 fork-exec 없이 즉시 파일 truncate 가능
  
- redirection: file descriptor
  - &#60;&&#35;: 표준 입력을 파일에서 특정 fd값 입력(&&#35; 생략시 0을 의미)
  - &#35;&#62;: fd &#35;번을 파일로 (&#35; 생략 시 1을 의미)
  - &&#62;: stdout, stderr를 파일로
  - &#62;&&#35;: 출력을 fd &#35;으로
  - a | b: a의 표준 출력은 b의 표준 입력으로 연결됨
  - tee 파일명: 파일로 저장되면서 화면에도 출력
  
- redirection: pipe
  - &#60; (cmd): anonymous pipe로 만들어 cmd 명령에 보내어 출력함(cmd 실행 결과는 임시 anonymous pipe인 /dev/fd/60으로 맵핑)
  - &#62; (cmd): cmd 명령 결과를 anonymous pipe로 보내어 읽어 들임(cmd 실행 결과는 임시 anonymous pipe인 /dev/fd/63으로 맵핑)
  - example
  
- redirection: HERE document
  - command &#60;&#60;SYMBOL NAME
    ... contents
    SYMBOLE NAME: symbol 이름을 지정하면 다음 symbol 이름이 나올 때 까지 중간에 입력하는 모든 문자열이 표준 입력(여러행을 표준 입력으로 사용할 때 사용하고 관습적으로 SYMBOL NAME을 HERE로 사용함)
  - 지금은 sftp를 많이 사용하면서 ftp command를 사용한 HERE document는 자주 사용하지 않음
  
- redirection: noclobber
  - redirection에 의한 덮어씌움 금지
  - force: ... >| FILE(강제로 해주기)
  - set -o noclobber
  - set +o noclobber(해제)
  
- redirection: code-block
  - {}로 묶어주고 redirection 해주면 전체가 redirection
  
### named PIPE(FIFO)
  - 여태까지는 이름없는 파이프(anonymous pipe)
  - 이름이 있는 파이프는 연속성이 있음
  - external interface가 있는 경우
  - https://sunyzero.tistory.com/260 
  - 파이프는 단방향 통신이기 때문에 반대편 파이프(peer)가 열려야 끝남
  - Practice: FIFO
    - tmux로 작동되는 다수의 시스템에 명령어 전달 및 피드백
    - tmux는 시스템에 가상 터미널 화면을 띄워놓고 client처럼 사용
  
# 12. 괄호들의 기능 총정리(확장된 표현식)
### 소괄호 (parentheses)
- 개수가 1개인 경우: (string)
  - string 문자열을 명령어로 인식
  - sub-shell을 만들어서 그 안에서 실행 
  - 서브셸에서 선언 or 변경된 값은 서브셸에서만 유효
  - string 앞뒤로 공백 허용
  
- 개수가 2개인 경우: ((string))
  - string에 해당하는 문자열을 수치 연산으로 인식(ksh에서 계승)
  - expr, let으로 대치 가능(expr은 외부 명령이므로 지양)
  - string 앞 뒤로 공백 허용
  
### 중괄호 (brace)
- {..., ..., ...}, {#-#}
  - brace expansion
  - shell에 있는 기본 기능
  - 콤마로 구분된 문자열 그룹, 혹은 숫자 범위를 지정하여 연속된 숫자
  - 공백 허용 안됨
  
- { ..; ...; }
  - shell에 있는 명령어를 그룹핑 시키는 기능
  - 현재 셸에서 실행
  - 마지막 세미콜론 생략 시 에러 발생

### 대괄호 (brackets)
- [...]
  - 배열 변수의 인덱스
  
- [ expression ]
  - 테스트 구문이나 숏 서킷 기능으로 사용( expression의 부분이 true, false를 테스트 )
  
### 괄호 예제
- $$: 현재 shell의 PID 값
- $BASHPID
- 앞에 -가 붙으면 login shell
- PPID와 PID를 보고 부모 자식 파악
- ps -o로 옵션 필드 지정 가능
- eval: 수식을 평가하는 작업(실행할 때)
- eval 안에 \를 사용해서 치환 및 실행을 방지
- 스크립트가 $$, $PPID를 파싱시키는 시점 중요 → eval로 수행 시점 변경

### Summary
- sub shell에서 실행할 때는 PID와 PPID를 혼동하지 않도록 주의
- 특히 sub shell에서 프로세스 제어 때문에 PID, PPID를 얻어야 하는 경우 있음
- 프로세스를 제어할 때는 signal로 보통 제어
- PID, PPID를 sub-shell에서 얻는 경우에 잘못 얻으면 심각한 문제 발생
- parentheses로 감싸진 sub shell 실행 코드 부분의 치환은 script가 실행하는 시점에서 결정됨(따라서 잘못된 값이 들어갈 수 있음)
- 이를 해결하기 위해서 eval로 실행 시점에 평가하도록 변경하면 해결이 쉽게 됨

### Practice: refactoring(성능향상)
- 1~100까지 합산하는 스크립트 (expr 사용)
  > sum='expr $iter + $sum'
  > iter='expr $iter + 1'

- 위 코드를 (())으로 변경
  > (( sum = iter + sum))
  > iter=$(($iter+1))
  > (( iter = iter + 1))도 가능
  
- 위 코드를 let으로 변경
  > let sum+=iter  
  > let iter++
  
- 명령 앞에 time을 사용하면 시간을 잼
  - real: 실제 시간
  - user, sys: cpu 시간
- expr의 외부 명령 사용은 상당한 성능 저하
- loop 안에서는 최대한 fork-exec 하지 않는 것이 좋음(fork-bomb 유발)
- loop 안에서 어떤 외부 명령어를 사용할 수밖에 없는 구조에서는 shell보다 python 같은 script 프로그램 사용이 용이

- bash: integer
  - 64 bit(2의 64승)
  - floating point를 인식하지 못함
  - 실수를 사용하면 bs나 awk도 가능
  - let, ((...)) 명령을 사용하면 fork-exec를 하지 않음
  
- let에서 사용할 수 있는 수치 연산 및 비교
  - ++ARG, --ARG: pre
  - ARG++, ARG--: post
  - !ARG, ~ARG
  - ARG**
  - <<, >>: bitwise shifts
  - <=, >=, <, >
  - ==, !=
  - &, |, ^
  - &&, ||
  - base#value: 진수 계산
  
- double-parentheses를 사용하면 C-Style로 코드를 구현 가능

### Practice: parentheses, let
- /proc/meminfo로 읽어오면 line 번호로 메모리 사용 여부를 알려줌
- 이것을 $2로 읽어서 넘겨주고 배열의 원소로 추가
- shell은 공백 오타가 많기 때문에 자주 실습해주기

### brace expend
- brace는 일반 명령에서도 많이 사용함
- brace 확장 기능을 사용하기 위해서는 옵션을 켜야함
- set -o | grep brace로 확인(default로 켜져있음)
- copy나 move 명령에서도 많이 사용됨
  - cp hello{,.backup} → cp hellp hellp.backup
  
- brace expand를 사용해서 {#..#}으로 for 문 성능 향상

### 확장된 변수 치환법
- 변수 값의 일부를 반환하는 기법
- 문자열 일부 삭제, 길이, 특정 pattern 치환법(정규 표현식은 아님)

- 변수의 값 길이를 알아내는데 사용하는 방법(양 옆의 중괄호 생략 불가능)
  - &#36;{&#35;varname}: varname의 문자열 길이를 의미
  - &#36;{&#35;arrayname[*]}: 배열 안에 요소 개수를 의미
  - &#36;{varname:offset}: 변수의 offset 위치부터 출력(0부터 시작)
  - &#36;{varname:offset:len}: varname의 문자열을 offset 위치부터 len만큼 출력(len이 음수일 경우 뒤에서부터 계산)
  - &#36;{arrayname[@]:offset}: offset, len을 지정해서 일부를 가져올 수 있음
  - &#36;{arrayname[@]:offset:len}: offset, len을 지정해서 일부를 가져올 수 있음
  
### Practice: variable: length
- 한글이 잘 인식하는지 간단한 코드 테스트 해보기

### Practice: variable: position
- uname -r: 시스템의 버젼 출력

### default value, alternative, err
- 변수를 사용하기 전에 값이 있는지 할당 되어있는지 확인해보는게 중요
- 변수가 한 번도 할당된 적이 없어서 변수의 값이 null 값이면 default 값을 할당하거나 error를 발생시켜서 사용(if 문으로도 사용 가능하지만 코드가 길어짐)
- &#36;{varname:=default}: varname이 null이면 default를 할당. 아니면 기존 값으로 치환
- &#36;{varname:-default}: varname이 null이면 default를 반환
- &#36;{varname:+alternat}: varname이 null이 아니면 alternat를 반환, null이면 그대로
- &#36;{varname:?error}: 변수 varname이 null이면 에러 발생 후 error 메시지의 값을 화면에 출력(스크립트는 retrun code로 1 보고), 화면에 출력할 뿐 저장하지는 않음
  
### variable: null test
- 스크립트 파일로 if 문 사용해서 찾기
- echo "${var_NULL:?}"

### 변수의 값을 찾아서 해당 매칭되는 부분에 값 자르기
- 후방 탐색으로 pattern과 매칭되는 varname의 값이 삭제
  - &#36;{varname%pattern}: 최대한 적의 수의 패턴 매칭(non-greedy)를 수행
  - &#36;{varname%%pattern}: 최대한 많은 패턴 매칭(greedy)를 수행

- 후방 탐색으로 pattern과 매칭되는 varname의 값이 삭제
  - &#36;{varname#pattern}: non-greedy 매칭 수행
  - &#36;{varname##pattern}: greedy 매칭 수행
  
- %, #를 이용하면 패턴과 매칭되는 공간을 삭제함

### pattern replacement: pat 패턴을 찾아 replace 교체(replace 부분 생략시 삭제)
- &#36;{varname/pat[/replace]}: non-greedy로 첫번째 패턴만 교체
- &#36;{varname//pat[/replace]}: greedy이므로 모든 패턴 교체
- &#36;{varname/#pat[/replace]}: varname 변수의 앞부분에서 pat을 찾아서 repl로 교체
- &#36;{varname/%pat[/replace]}: varname 변수의 뒷부분에서 pat을 찾아서 repl로 교체
- &#36;{!varprefix*}: varprefix의 prefix 이름을 가진 변수 모두를 선택
- &#36;{!varprefix@}: varprefix의 prefix 이름을 가진 변수 모두를 선택

### glob patterns
- character
  - ?: 싱글 캐릭터 (임의의 문자 한개)
  - *: 공백을 포함하는 문자열
  
- classes
  - [...]: 원소(bracket 안의 문자 1개)
  - [!..]: 여집합(complementation )
  
- **extglob**
  - ?(pattern): 0~1번 pattern 발생
  - *(pattern): 0번 이상 pattern 발생
  - +(pattern): 1번 이상 pattern 발생
  - @(pattern): 1번 pattern이 발생
  - !(pattern): pattern이 발생하지 않음
  
- OR
  - A|B: A 혹은 B
  
### Practice: variable: glob
- replace 문자를 지정하지 않으면 삭제로 작동
- @로 occurence를 제한 가능
- 대부분 간단한 표현식은 여러가지 방법으로 표현 가능
- REGEX(정규표현식)도 중요

### Practice: filename: glob
- 파일명 패턴도 glob를 중요
- touch 파일을 갱신시키거나 파일명이 없으면 0byte 파일 만들기

- 연습 1) 2021년 짝수 달의 파일만 선택하기
> &#36; ls archive_2021@(02|04|06|08|10|12)*.tar.xz
>
> &#36; ls archive_2021@(0[2468]|1[02])*.tar.xz

- 연습 2) 2021년 1, 2 숫자가 들어간 월, 일을 제외한 파일만 선택하기
> &#36; ls archive_2021+([!12]).tar.xz

- 연습 3) 2021년 짝수 달의 7, 28일의 파일만 고르기 위해서는?

### Summary(typeset, declare)
- -a [name[=value]]: 배열 변수를 선언함(-A 사용시 associative array 사용)
- -f [name[=value]]: 함수를 선언함(보통 생략)
- -F [name[=value]]: 선언된 함수명을 표시함(인수가 생략시 현재 함수 리스트 출력)
- -i [name[=value]]: 정수형 변수를 선언함
- -r [name[=value]]: 변수를 readonly 속성으로 만듬
- -x [name[=value]]: 일반 변수를 환경변수로 export 함
- -p [name]: name 생략시엔 변수 목록을 표시하며, name이 주어지면 변수의 속성과 값을 출력
- +{aAfirx} name: 해당 변수의 속성을 해제함(on을 할 때는 -, off을 할 때는 +)

### Practice
- 변수의 속성을 설정/해제, 프린트

### variables: name
- 여태까지는 변수의 값을 읽어오기
- 이제 변수의 이름을 읽어오기
- &#36;{!varname*}, &#36;{!varname@}: varname으로 시작하는 변수명 반환(*의 경우 ""으로 묶으면 IFS의 첫번째 문자열을 구분자로 사용함)
- &#36;{!arrayname[*]}, &#36;{!arrayname[@]}: arrayname이 indexed array인 경우에는 index 번호 반환, associative array인 경우에는 key 배열 반환

# 12. 정규표현식(REGEX)
### 배울 것들
- Regular Expression(정규 표현식)의 약칭
- string pattern: 문장열의 조합되는 규칙
- meta chareter: 다른 의미를 수식하는 문자(예약어)
- grep: 정규식을 평가할 수 있는 유틸리티
- sed: 스트림 에디터
- awk: 패턴식을 다룰 수 있는 언어툴

- String, pattern
- 문자열 중에는 일정한 규칙이 존재하는 경우가 있음
  - email 주소인 경우
  - Web URL의 경우
  - IPv4 주소의 경우
  
- REGEX: POSIX, PCRE
- REGEX는 여러 변종이 있음
  - POSIX REGEX: UNIX 계열 표준 정규표현식
    - BRE(Basic RE)
      - grep이 작동되는 기본값
    
    - ERE(Extended RE)
      - 좀 더 많은 표현식과 편의성을 제공
      - BRE와 ERE는 같은 기능을 같고 있는데 ERE에서 사용하려면 약간의 기능을 추가해야 함
    
  - PCRE(Perl Compatible Regular Expression)
    - Perl 정규표현식 호환으로 확장된 기능
    - C언어 기반이며 POSIX REGEX에 비해 좀 더 성능이 좋음
    - 현재 PCRE2 버전을 사용
    - 실무에서 자주 사용
  
  - EBNF 문법
    - *, +, ?, [...]는 EBNF의 영향이 큼
    - Extended Backus-Naur Form
    - 필수로 알아두는 것이 좋음
  
### grep
- 정규 표현식을 테스트하거나 필터링하는 툴 3가지
  - grep
    - global regular expression print
    - 유닉스에서 가장 기본적인 REGEX 평가 유틸리티
  
  - sed
    - stream editor
    - REGEX 기능을 일부 탑재하고 있음
  
  - awk
    - REGEX 뿐만 아니라 문자열 관련의 방대한 기능을 가진 프로그래밍 언어
    - 가장 많은 기능
    
  - grep → sed → awk 순으로 배우는 것이 좋음
  
- matcher selection
  - grep 실행 시 matcher를 고를 수 있음
  - -G
    - BRE(POSIX BRE)를 사용하여 작동(기본값)
  
  - -E
    - ERE를 사용하여 작동(egrep으로 작동시킨 것과 같음)
  
  - -P
    - PCRE를 사용하여 작동(pcre2grep으로 작동시킨 것과 같음)
    
  - -F
    - 고정길이 문자열을 탐색하는 모드로 작동(fgrep과 같음)
    - 거의 사용하지 않음
  
- grep 주요 옵션
  - --color: 매칭에 성공한 부분을 강조 색깔로 칠해줌
  - **-o**: 매칭에 성공한 부분을 잘라서 알려줌(원래 grep은 line 전체를 보여줌)  
  - -e PATTERN: 패턴을 연달아서 사용
  - **-v**, --invert-match: verbose나 version이 아닌 검색에 실패한 부분만 뽑아냄
  - -c: output count
  - -q, --quite: 성공, 실패만 알려주기
  
### REGEX syntax
- 특수 예약어 기능(ERE가 아니면 BRE)
  - 문자 지정
    - .: 임의의 문자 한 개를 의미
    
  - 반복 지정(수량자(Quantifier), 선행문자 패턴(atom))
    - ?: 선행문자패턴이 0개 혹은 1개를 나타냄(ERE)
    - +: 선행문자패턴이 1개 이상 반복됨(ERE)
    - *: 선행문자패턴이 0개 이상 반복됨
    - {m,n}: (interval) 반복수를 직접 지정할 수 있음(ERE)
      - m이 생략하면 0, n이 생략하면 무한대
  
    - grep은 기본값으로 BRE로 작동하므로 +, ?, {} 패턴은 앞에 \(back-slach)를 더해줌
    
  - 위치 지정(Anchor)
    - ^: 라인의 앞부분 의미
    - $: 라인의 끝부분 의미(개행문자 단위로 처리하지 않는 경우 문서의 끝을 의미함)
    - ^$: 비어 있는 라인
      
  - 그룹 지정(Character sets)
    - [...]: 안에 지정된 문자들 그룹 중에 한 문자를 지정함
    - [^..]: 안에 지정된 그룹의 문자를 제외한 나머지(여집합)를 지정함
    
  - 기타
    - \: (escape) 메타의 의미를 없애줌
    - |: (alternation, choice) OR 연산(ERE)
    - ( ): 패턴을 그룹화 및 백레퍼런스의 작동(ERE)
  
  - Practice: POSIX BRE
    1) REGEX로 단어 검색
    2) anchor를 이용해서 패턴 수정
    3) log data 검색
  
  - grep: line control options
    - -A NUM, --after-context=NUM: after
    - -B NUM, --before-context=NUM: before
    - -C NUM, -NUM, --context=NUM: A와 B
    - --group-separator=SEP: 중간 separator 기본적으로 하이픈 2개
  
### greedy
- Greedy matching
  - pattern이 최대한 많은 수의 매칭을 하려고 하는 성질
  - 표현식을 만들 때에는 greedy matching 후 result set의 범위를 줄여나가면서 정확한 표현식을 완성하도록 해야 함
  
- Non-greedy matching
  - 최소 매칭 기능
  - greedy matching의 반대 개념
  - PCRE에서는 non-greedy matching을 쉽게 사용할 수 있는 수량자를 제공하지만 POSIX RE에서는 제공하지 않음
  - vim에서는 POSIX BRE를 사용하지만 non-greedy 수량자 \{-}를 제공
  
- Non-greedy matching: vim
  - /<.\{-}>
  
- Non-greedy matching: lazy quantifier
  - PCRE 기능
  - suffix로 ? 더해주기
  - PCRE에서만 지원하므로 POSIX REGEX 모드에서 사용할 수 없음
  - grep -P -o "<.+?>"
  
- Non-greedy matching
  - POSIX
    > &#36; echo $var2 | egrep -o "<[^<>]+>"
    
  - On PCRE
    > &#36; echo $var2 | grep -P -o "<.+?>"
    > 

### bachslash
- bach-slash의 기능
  - meta char(.)의 기능을 없애는 기능(진짜 일반 문자를 의미하게 함)
  - BRE에서 ERE의 일부 기능을 표현할 때 사용
    - ERE의 {m,n}을 BRE로 표현할 때 \{m,n\}으로 사용
    - ?, +, {}, |, ()에 대해 역슬레시를 사용(BRE에서 ERE 기능을 사용할 때)
    - egrep을 사용하는 경우 역슬레시 필요 없음
  
  - back-slash를 문자 앞에 두는 행동을 escape 시킨다고 표현
  - Practice: back-slash
    - grep이 아니라 egrep을 사용하면 굳이 back-slash를 사용하지 않음
  
  - Practice
    - URL 링크를 추출하기
      - curl: 터미널에서 웹 페이지를 접속하는 유틸
      - sort | uniq 사용하기
      - "<img [^<>]+>"
  
### BRE vs ERE
- 어떤 툴이 BRE를 사용하고 ERE를 사용하는지 알아두면 언제 back-slash를 붙여야 하는지 쉽게 판단
- BRE를 사용하는 유틸: grep(기본값), vim, sed, 과거에 만들어준 프로그램
- ERE를 사용하는 유틸: egrep, awk, sed+option
- Practice: ERE를 BRE로 바꿔주기
  - ERE: "<img [^<>]+>"
  - BRE: "<img [^<>]\+>"
  
### 소괄호
- () 괄호는 여러가지 기능으로 사용
  - back-reference
    - 매칭된 결과를 다시 재사용하는 패턴
    - "()"에서 묶인 패턴 매칭 부분을 "\#"의 형태로 재사용
    - (#는 숫자가 순서대로), 0번은 전체 매칭 결과
    - options
      - v: invert
      - --color
      
  - back-reference 응용: tag로 감쎠여진 부분 추출
    
  - Alternation
    - alternation이나 pattern group을 묶을 때에도 사용
    - Practice: <a> 태그만 골라내기(ERE/BRE)
      - BRE에서는 ERE meta character인 ?, +, {}, (), |를 escape 시켜야함
    
    - Practice: URL 링크 추출해보기(jpg, png)
  
### 교체(Substitution)
- sed, awk로 교체 작업을 하는 경우가 많음
- sed
  - sed에서 제일 많이 쓰는 기능이 substitution
  - vim의 교체 기능과 같음
  - -r: ERE로 교체
  
- awk
  - gsub(global sutstitution)에서 REGEX로 교체하는 방법
  
- Practice: URL 링크 제거(sed 사용, awk 사용, ERE 사용, BRE 사용)

### Boudnary - ERE
- word 경계 검색에 사용
  - \b: boundary에 맞는 표현식만 찾음(단어 경계면 검색)
  - \B: boundary에 맞지 않는 표현식만 찾음(단어 경계면에 아닌 경우만 검색, 어떤 문자 안에 있는 경우만 찾아줌)

### Predefined character class
- **[[:cntrl:]]**: 제어문자들을 의미 → [\x00-\x1f\x7f]
  - \x##의 hex code 표현은 PCRE에서만 지원
- **[[:space:]]**: white space(공백으로 사용되는 6개의 문자) → [ \t\r\n\v\f]
- [[:print:]]: 출력 가능한 문자들 (제어문자를 제외한 문자) → [ -~]
- [[:graph:]]: 공백을 제외한 출력 가능한 문자들 → [!-~]
- **[[:punct:]]**: 출력 가능한 특수문자들(punctuation 기호) → [!-/:@\[-`{-~]
- [...] 안에 조합 가능

- Practice
  - IPv4 address를 REGEX로 표현하기
    - or 연산으로 잘라서 표현하기
  
- Practice: Hangul, Hanja
  - i18n을 만족하는 Linux는 UTF-8 한글 처리도 가능

- Practice: Hangul class
  
- REGEX tester: 터미널이 없을 때 웹 페이지에서 REGEX를 테스트하는 것
  - https://regexr.com/
  - https://www.regextester.com/
  - https://regex101.com/

### REGEX and PCRE
- POSIX REGEX(BRE+ERE)
  - 간단한 패턴 매칭에 사용
  - 패턴의 복잡함이 늘어나면 성능 저하가 발생
  - POSIX REGEX가 국제 표준이기 때문에 먼저 배움
  
- PCRE
  - Perl에서 파생된 확장된 정규표현식
  - POSIX REGEX를 포함
  - 매우 빠른 속도
  - 실무에서 많이 사용
  
### Glob
- UNIX나 Linux 명령행에서 파일명 패턴에 쓰이는 문자열
- REGEX와 비슷하지만 REGEX는 아님
- man 7 glob로 살펴보기
- Web-based glob tester: http://www.globtester.com/

### backtracking
- Greedy matching 속성으로 인해 역탐색을 하는 행위
- 성능을 떨어뜨리는 요인
- 성능이 매우 중요하고 filter 같이 빈번하게 regex test를 하는 구조라면 backtracking을 제거하는 것이 좋음
- regex101.com에 접속해서 REGEX 아래 문자열을 넣어서 성능 확인하기
- Log 메시지를 이용한 backtracking 확인
  - .을 사용하면 greedy
  
- backtracking을 피하려면
  - .* 혹은 .+과 같이 greedy한 표현식을 남발하지 않기
  - . (dot)은 되도록이면 쓰지 않거나, 꼭 사용한다면 lazy quantifier를 사용하는 것이 좋음
  - 공백을 반복할 수 있는 표현식을 피하기
  
- backtracking으로 손실되는 성능
  - non greedy한 표현식보다 100~1000%의 성능 하락이 있음
  
# 13. PCRE
- PCRE
  - Perl Compatible Regular Expression
  - PCRE2: version 2.x
  - C로 작성됨
  - Document: https://www.pcre.org/original/doc/html/pcrepattern.html
  - Debain package: apt -y install pcre2-utils
  
- Tools
  - PCRE v2: pcre2test, pcre2grep
  - grep은 기본적으로 pcre mode를 갖고있음(grep -P ...)
  
- 새로운 syntax
  - Class(abbreviation)
    - \d: [0-9] decimal digit
    - \h: [\t\x20] horizontal white space
    - \v: [/n/v/f/r] vertical whilte space
    - \w: [a-zA-Z0-9_] word character
    - \s: [\t\n\v\f\r\x20]: \h와 \v를 더한 값
    - \D, \H, \V, \W, \S: 대문자로 표기하면 여집합의 의미(complementation)
  
  - POSIX REGEX vs PCRE
    - POSIX REGEX에서 실패하는 표현식이 PCRE mode에서는 성공
    > grep -o "^\D+" (POSIX REGEX에서 실패)
    > grep -Po "^\D+" (PCRE mode에서 성공)
    > pcre2grep -o "^\D+" (위와 같은 기능)
    
  - Quantifier
    - lazy quantifier: *?, +?, {n,}?
  
  - look-ahead, look-behind
    - 탐색하려는 패턴의 앞, 뒤에 살펴보기만은 restriction 제공
    - 패턴을 찾고나서 리턴할 결과에는 포함시키지 않음
    - look ahead: 패턴을 찾고 패턴의 앞 부분에 무엇이 있는지 제약(없는 경우는 무시하고 제약이 있으면 return)
      - positive: 존재하는지 확인(pattern_to_find(?=look))
      - negative: 존재하지 않는지 확인(pattern_to_find(!=look))
      
    - look behind: 먼저 패턴을 찾고 되돌아가서 look할 것이 있는지 찾기
      - positive: 존재하는지 확인((?<=look)pattern_to_find)
      - negative: 존재하지 않는지 확인((?<!look)pattern_to_find)
    
    - look 부분의 패턴은 고정 길이만 지원
      - (?<=prefix:)\w+는 가능, (?<=prefix:+)\w+는 불가능
      
    - $같은 특수 문자를 사용하려면 앞에 \를 붙여줘야함
    - look-ahead/behind는 boundary와 같이 쓰는 경우가 많음(앞뒤로 \b 써주기)
    
  - Position
    - Front: \A
      - 문서의 맨 앞부분과 매칭(^와 같은 기능)
    
    - End: \Z
      - 문서의 맨 뒷 부분과 매칭 (일반적으로 $와 같으나 multiline mode에서는 문서의 끝 부분(마지막행)을 의미)
  
  - Internal option setting
    - (?imsx)
      - i: ignore case(대소문자 구분 X)
      - m: multiline mode(하나의 행 단위가 아니라 전체 문서를 연결된 행으로 보기)
      - s: dot all(임의의 문자(new line을 포함))
      - x: extended regex(ERE를 사용)
      - 옵션을 끄기 위해서는 앞에 -붙이기
  
- PCRE2 in C
  - POXIC C API compatible
  - regcomp, regexec, regerror, regfree 함수 형태 그대로 사용 가능
  - regex.h 헤더 대신아 pcre2posix.h로 교체하고 링킹 옵션 -lpcre2-posix 추가
  - 더 고급 기능을 사용하기 위해서는 API 사용
  
- Non-printable characters
  - \a: alarm
  - \t: tab
  
- pcre2grep
  - pcre를 설치하면 같이 설치되는 기능
  - gerp -P은 호환성 모드로 인해서 모든 기능을 제공하지 않아서 pcre2grep은 PCRE2의 모든 기능 제공
  - -M: multiline mode
  - ^: 행의 시작 부분, $: 뒤에 아무것도 오면 안됨
  - ^을 \A로 변경하면 변화없음
  - $을 \Z로 변경하면 \Z가 문서의 끝이므로 multi-line 모드에서는 new line이 아니라 EOF를 의미하게 됨(multiline 모드가 아닌 경우에는 같은 의미)
  - dot all을 쓰지 않고 사용하려면
    - "(?S)/\*.+?\*/" → "/\*[ -~\s]+?\*/"
    - .은 greedy matching을 만들어서 크게 작업하고 session을 줄여나가기 (초기에만 많이 사용하고 되도록 사용하지 않는 것이 좋음)
  
- pcre2test
  - 여러가지 복잡한 프로그램을 테스트해보는 기능
  - 기능이 굉장히 많음
  - re> 여기에 typing하기
  - data> 여기에 적은 것으로 정규표현식을 테스트해줌
  - empty string을 입력하면 RE를 다시 입력할 수 있음
  - man page가 복잡한 편이지만 나중에 사용하기 좋음
  - callout 기능을 이용하여 단계별 말풍선을 볼 수 있음
    - callout_no_where, callout_extra
  
  - pcre2test [input file [output file]]
    - input file을 test해서 output에 기록
    - output file이 생략되면 stdout으로 출력
    - input: RE + Data
    - 중간에 blank line이 있으면 RE를 새로 입력하는 것으로 간주
    - grep -v '^[[:space:]]*$'를 앞에 사용
    - stdin으로 받는 경우 -를 input file로 지정함(하지만 생략 가능)
      
# 14. sed
### ed
  - vi 이전의 edit
  - line editor
  - ed command mode
    - a: append
    - . & enter: end of editing
    - w: writing
    - q: quit
    - %p, ,p: print
    - wq: write, quit
    
  - vi command line과 비슷함
  - ed의 문제점
    - stream으로 처리하는 기능이 부족
    - sed의 다양한 기능 추가
  
### sed
- test filter로서의 특징
  - 가볍고 빠른 교체 기능 제공
  - ed의 명령어 집합을 개량하여 만들어짐
  - 원본ㅇ늘 변경하는 방식이 아니라 입력 스트림으로 받아서 조작 및 변경한 뒤 출력 스트림을 내보냄
  - vim의 substitute 명령은 sed와 동일
  - 정규표현식은 POSIX BRE를 사용함(-r 옵션은 ERE를 사용할 수 있게 함)
  - 처음 배울 때는 BRE로 배우고 실무에서는 ERE 옵션 적극 활용
  
- sed command
  - 커맨드 라인: sed 'expression cmd' input_file [file ...]
  - 복수의 cmd: sed -e 'cmd1' -e 'cmd2' ... input_file [file ...]
  - sed script: sed -f sed_script_file input_file [file ...]
  - -n: 특정 결과 행의 출력이 print 관련 명령을 만날 때 출력함(사용자가 출력을 커스터마이징 하거나 바뀐 행만 출력하고자 하는 경우에 사용)
  - -e: expression script (1개인 경우는 생략가능)
  - -f: sed 스크립트 파일 지정
  - **-r**: REGEX matcher를 ERE로 사용(default는 BRE)
  
- seb: substitute(교체)
  - sed -e 찾을 문자열 교체할 문자열
  - g 옵션을 주면 한 행에 여러 번 나와도 계속 교체
  - 구분자는 관습적으로 / , | $ %을 많이 사용하는 경우가 많음
  - 복수의 표현식을 처리하기 위해 sed를 여러번 호출하는 것은 비효율적(-e를 여러번 사용 가능해서 해결)
  
- sed cmd
  - ed의 명령과 같음
  - sed는 간단한 작업, 복잡한 작업은 awk를 사용해줌
  - a<text>: append, 선택된 행 뒤에 text 추가
  - c<ch_text>: 선택된 행을 ch_text로 교체
  - d: 선택된 행을 삭제
  - g: 현재 행을 버퍼의 내용으로 대체
  - h: 현재 행을 버퍼로 복사
  - p: 행을 출력(-n과 같이 쓰임)
  - q: 편집 스크립트 종료
  - r<file>: 파일 읽어들임
  - s/old/new/func: old를 검색해서 new로 교체(/대신 다른 구분자 사용 가능)
    - func: g(global), p(print), w(write), n(1~512정수), i(ignore case)
  - y/old/new: old를 new로 교체(단 길이가 같아야만 함)
  - =: 현재 입력행의 번호를 표준 출력으로 인쇄
  - !sed)cmd: 패턴에 부합하지 않거나 범위로 지정되지 않은 행에 대해 sed_cmd 수행
  - /REGEX/: range 선택 시 정규 표현식 사용
  
- sed: practice1
  - 행 자체를 교체
  > $ sed '/nologin$/cNO_LOGIN_USER' /etc/passwd

  - 복수의 공백을 1개의 공백으로 치환
  > $ sed 's/ */ /g' infile
  
  - 공백 라인의 제거
  > $ sed '/^$/d' infile
  
  - 파일을 여러 개로 분리하기(/w 사용, -e 여러번 사용)
  
- sed -i
  - 일반적으로 사용하는 sed는 편집한 스트림을 저장하지 않고 stream으로 출력함
  - -i 옵션을 사용하면 바로 저장할 수 있음
  - 백업 파일을 만들고 싶으면 sed -i.bak 사용
  
- sed -r
  - r: ERE를 사용하는 옵션
  
- sed -z
  - 일반적으로 sed는 NewLine 단위로 처리
  - 끝이 NewLine 대신에 NUL이 들어간 경우 사용
  
# 15. awk
### awk(오크)
- 설계자의 머릿 글자에서 유래
  - Alfred V. **A**ho
  - Peter J. **W**einberger
  - Brain W. **K**ernighan
  
- 공식적으로 '패턴 검색과 처리언어'라고 정의
- 독자적인 처리 문법과 언어 구성을 갖추고 있음
- expr, grep, sed의 모든 기능을 포함하고 있으며, filed 처리 가능
- grep, sed로 처리 불가능하다면 awk 사용 검토

### awk 실행 방법
- awk [-F 필드구분자] '스크립트코드' <처리할 파일> ...
- awk [-F 필드구분자] -f <awk스크립트파일> <처리할 파일> ...
- 필드 단위의 구분이 가능함 (csv 파일에 ,를 넣어주면 구분 가능, 공백이나 tab키도 모두 지원)

### awk CLI
- 필드를 구분할 때 awk에 필드 구분자를 알려줘야 함
- 필드 구분자를 알려주지 않으면 space나 공백을 필드 구분자로 인식
- -F: #field separator

### awk script block
- 조건/패턴이 true일 때 { command; ... } 실행
- [ condition or pattern ] { command; ... }
- NR: 내부 빌트인 변수로 Line number
- NR>1: 라인 넘버가 1보다 큰 경우

### Printf: C-style formatting
- %[flag][x][y.]c
  - flag: 플레그 지정(-, 0, ')
    - -: 왼쪽으로 정렬
    - 0: 숫자 출력시 빈 공백 부분을 0으로 채움
    - ': 숫자 출력시 천(1000) 단위로 점을 찍을 때 사용
      - printf에서 안에 홀 따옴표를 지정할 때 매칭에 주의(escape를 사용해서 해결: '문자열'\''문자열'')
  
  - x: 최소 필드 너비(min. field width)를 지정
  - .y: 정밀도 지정
  - c: 변환형 지정(s,d,f,s,c,...)
  
- printf: practice

### awk field를 swap 하는 기능
- $#으로 swap 가능
- awk는 real number도 지원

### Practice

### awk pattern
- pattern { ... }
  - { ... }안에는 정규 표현식
  - 정규 표현식에 만족하는 연산만 작동
  - regular expression
    - realtional expression
    - pattern && pattern
    - pattern || pattern
    - pattern ? pattern : pattern
    - (pattern)
    - ! pattern
    - pattern1, pattern2
  
- /REGEX/를 이용하여 조건 사용
  - opterator
    - ~: match
    - !~: not
  
  - $0: 전체 line
  - /^[^#]+/: 행의 첫 시작이 #만 아닌 경우
    - ^: 행의 맨 처음 시작부분
    - [: 집합
    - ^: 여집합
    - #: 해시
    - +: 1개 이상
  
### BEGIN, END
- BEGIN: 실제 연산이 일어나기 전에 수행
  - initialization
  - pre-processing
  
- END: 연산이 끝난 뒤에 수행(주로 출력 수행)
- BEGIN, END를 이용한 계산
  - BEGIN 쪽에 print문
  - 실제 실행 부분
  - END 쪽에 적분을 이용해서 pi값을 구하는 코드
  
### built-in var
- 환경변수에 해당하기 때문에 대부분 대문자
- **ARGC**: 커맨드 라인 아규먼트의 개수
- **ARGV**: 커맨드 라인 아규먼트의 어레이
- **FS**: 입력 필드 seperator 문자
- **NF**: 현재 레코드의 필드 번호
- **NR**: 현재의 레코드 번호(모든 입력 파일을 통틀어)
- FIELDWIDTHS: FS 대신에 사용할 고정 필드 크기(구분자가 없는 데이터 처리시 사용함)
- **OFS**: 출력 필드 seperator(기본값 스페이스)
- **RSTART**: 매칭 연산을 만족하는 문자열의 맨 앞부분
- **RLENGTH**: 매칭 연산을 만족하는 문자열의 길이
- **IGNORECASE**: 대소문자 무시 *

### flow control
- C style과 비슷

### print, var control
- print
- printf
- sprintf
- variable=awk_expr

### Practice
  - 합산 계산, 새로운 필드 할당
  - 2번째 과목의 평균 계산
  - System daemon의 Minor pagefault, RSS 계산
    - user가 "root"이고 ppid가 1이면 daemon(원래는 tty도 확인해줘야 함)
  
### function: length(x)
  - x의 길이 리턴

### function: String
  - gsub(r,s,[,t]): 정규표현식 r과 매치되는 문자열 t를 s로 대치(한 행에서 여러번 일어나도 전부 대치), t를 지정하지 않으면 전체($0)
  - sprintf(fmt,awk_exp): formatting 된 것을 저장해줌
  - sub(r,s,[,t]): 정규표현식 r과 매치되는 문자열 t를 s로 대치(한 행에서 한 개만 대치)
  - **gensub(r,replace,how[,t])**
    - gsub와 sub의 general version
    - 정규표현식 r과 매치되는 문자열 t를 replace로 변경하며, how에 지시된 사항을 따름(t를 지정하지 않으면 $0)
  - substr(str,start,length): str에서 start부터 length 만큼 자르기

### function: math
  - int, rand(), srand, cos, sin, atan2, exp, log

### function: date, time
  - systime(): UNIX epoch timestamp(1970.1.1 00:00:00)
  - strftime([fmt [, timestamp]]): C-style strftime

### Prctice: function
  - substr - substring
  - match
    - POSIX awk에서는 {n,m} 표현을 지원하지 않음
    - $ gawk --re-interval 옵션 추가(REGEX intervel expression 지원)
  
  - gensub(gsub, sub보다 더 많이 사용)
    - gensub(regex, replace, how [,target var.])
    - how
      - g: global
      - index: replace the n-th field
    
  - source code 생성(test_posixopt.txt)
    > man posixoptions | col -b | egrep -o "\b_POSIX_[A-z_]+\b" | sort | uniq
    
  - makefile을 이용하여 쉽게 생성
  - build and code

### shell variable
- 셸에서 작업하다가 셸 변수를 awk에서 작업하는 경우가 있음
- 그냥 사용하면 안되고 awk 변수로 바꿔주어야 함
- -v varname="$shell_var"

# 16. Debugging, Verbose
- xtrace
  - &#35;! /bin/bash -x
  - 명령행의 실행 후 값과 결과를 출력하여 값의 변화를 추적할 수 있게 해줌
  - on: set -x
  - off: set +x
  
- verbose
  - &#35;! /bin/bash -v
  - 명령행의 소스를 그대로 출력함(실행 흐름을 알 수 있게 해줌)
  - on: set -v
  - off: set +v
  
- Practice: xtrace

- 시그널을 이용한 xtracing, verbose
  - 두 가지 기능을 같이 끄고 켜고자 한다면
  - trap "set -xv" SIGUSR1: SIGUSR1이 실행되면 set -xv 실행
  - tarp "set +xv" SIGUSR2
  
- trap
  - trap (command | function) (SIGSPEC ...)
  - command, function: 시그널이 캐치되면 작동될 명령 or 함수
  - 명령어가 간단하면 command
  - 명령어가 복잡하면 function
  - SIGSPECT: 트랩을 설치할 시그널 or 조건
    - EXIT: 종료 핸들러(shell, subshell이 종료할 때 호출)
    - ERR: 에러 발생시(return value가 non-zeor일 때)
    - DEBUG: 명령실행 전에 매번 발생
    - RETURN: 함수 및 module file(source로 import했던 파일)에서 리턴될 때 발생
      - set -o functrace로 옵션 활성화 시켜야만 사용 가능
  
  - trap은 shell이나 subshell에는 적용되나 child에는 상속되지 않음
  
  - trap의 선언 위치
    - function 안에서 선언되면 해당 function scope에서만 유효
  
  - trap -p: trap list 출력
    
  - signal event 예제
    - ``(backtick)은 $()로 바꿔줘서 사용
    - trap을 비워두면 무시, blocking 한다는 의미
    - 양쪽을 ()로 묶으면 subshell로 작동되므로 조심해야 함 → {}로 묶어줘서 현재 shell에서 실행(뒷부분에 ; 항상 넣어주기)하거나 괄호를 아예 삭제하기
    - man 7 signal로 살펴보기
    - sleep 1 대신에 read -t 1(1초 대기 타이머 설정)로 처리하면 fork-exec를 막을 수 있지만 background 실행 시 SIGTTIN 발생하므로 좋은 방법이 아님
    - SIGTTIN, SIGTTOU 알아보기  
  
  - pkill, pgrep
    - process의 이름만 알고 pid를 모르는 경우 kill을 쓰려면 pkill이나 pgrep으로 검색
    - ./trap-1.sh로 실행하지 않고 bash trap01.sh로 실행했다면 패턴 검색이 안되므로 pkill에 -f 옵션 추가해서 사용해야 함
  
- Quiz 
  - SIGTTIN, SIGTTOU 시그널에 대해서 조사하기
    - TTIN, TTOU의 의미
    - 위 시그널은 어떤 경우에 발생하는지
    - 위 시그널이 발생하면 기본동작은 Term, Core, Stop 중에 어떤 것?
  
  - Daemon 프로세스들이 stdin, stdout, stderr을 /dev/null로 연결하는 이유와 SIGTTIN, SIGTTOU의 관계를 생각해보기
    - background에서 작동하는 daemon이 터미널에 입출력을 발생시키면 SIGTTIN, SIGTTOU가 발생하기 때문에 daemon은 stdio를 /dev/null로 연결하게 되는 것
  
# 17. sub-shell
### exit code
- subshell이 제대로 작동했는지 확인
  - Case 1: child가 성공적으로 종료되면 exit code는 0
  - Case 2: sleep이 종료되기 전(30초 지나기 전)에 다른 terminal에서 pkill sleep 명령을 해보면 exit code는 143
  
- exit status(exit code는 bash script에서만 가능)
  - echo $?는 exit code이지만 signal로 종료한 경우에는 128+<signal number>로 작동
  - 0: success
  - 1: error
  - 128+N: signal N일때
  - $?는 바로 이전 명령어에 대해서 알려주므로 다른 것을 바로 위에 사용하지 않도록 주의(그래서 변수에 exit status를 저장해서 사용하는 경우 많음)

- sleep 30 &: background running으로 작동
- child_pid=$!: background의 pid 값
- wait PID값: background를 기다림(blocking이 일어남)
  - wait를 하기 전에 pid가 종료되었다면 넘어가고, 아직 running 상태면 끝날 때까지 대기
  
- rsyschk.sh 예제
  - 특정 서버에서 특정 process가 돌고있는지 확인하는 예제
  
### errexit
- 리턴 값이 non-zero가 발생했을 때 프로세스 종료
- -e(default는 꺼져 있음)
- set +o errexit로 디폴트 설정 확인해보기
- &#35;!/bin/bash -e로 켜주기
- bash에서 critical section 처리 시 에러가 절대 나면 안되는 작업이면 set -e 옵션을 켜주고 작업하기(critical section 작업이 끝나면 set +e로 다시 켜주기)

- background job
  - errexit 기능은 foreground에 대해서 확인하는 것
  - background 작업은 $?에 값을 저장하지 않으므로 set -e에 영향을 받지 않음

- Practice: errexit
  - test_errexit.sh
    - -f 옵션: force, 어떤 명령을 실행할 때 명령을 강제함 (return 값을 상관하지 않음, error가 뜨지 않음)
    - force 옵션은 자식 프로세스의 return value를 조작하는 기능으로 사용되기도 함
  
# 18. shell option
### Shell option vs bash option
- bash에는 2가지 options가 존재
- set 계열(set -o errexit)
  - allexport: 모든 변수를 export
  - braceexpend: {...} expansion
    - set -B(default로 켜져 있음)
    - 숫자로 생성하여 seq 대신 사용 가능
    - expr은 성능이 안좋으므로 사용 X, let이나 (())로 교체)
    
  - emacs : emacs prompt mode
  - errexit: 에러 발생시 스크립트 종료
  - errtrace: ERR trap 상속
  - functrace: DEBUG trap 상속
  - hashall: 명령어 해시 테이블 기능
    - hash command로 해시된 명령어 리스트를 볼 수 있음
    - hash: display hash table
    - hash -r: clear hash
    - hash cmd: hash command from path
    - hash -p fullpath cmd: "" with fullpath
    - type cmd: query type of cmd
    - a라는 프로그램의 여러가지 버전이 있을 때 종료

  - histexpand: history interaction 기능
    - !!: 바로 이전 명령
    
  - ignoreof: EOF를 만났을 때 종료 여부
  - monitor: job control(fg, bg) 기능
  - $-: 현재 상태를 알 수 있음(interactive mode, script mode 등)
  - default로 켜져 있는 기능($-로 변화 확인하기)
    - h: hash table 
    - i: interactive mode
    - m: monitor
    - B: breaceexpension
    - H: history expansion

- shopt 계열(shopt -s autocd)
  - bash 전용 옵션
  - -s: set option
  - -u: unset option
  - -q: quite mode(화면에 출력하지 않기)
  - -p: print(현재 상황 print)

  - shopt options
    - autocd: 디렉토리 이동할 때 cd 명령을 사용하지 않고 디렉토리명만 입력해도 이동하는 기능
    - huponexit: SIGHUP(세션이 닫힐 때 child process한테 날릴 signal)
      - 예전에는 default로 set이었는데 지금은 unset
      - systemd가 도입된 이후로는 logind는 default 설정시 세션 종료시 백 그라운드 프로세스를 종료시키지 않음
      - logind가 관리하기 때문에 shell이 관리할 필요없음
      - login.conf에서 KillUserProcess 설정을 하면 logout 할때 자동으로 SIGHUP(추천하지 않음)
      
    - login_shell: login_shell인지 확인

- set, shopt option 주요 옵션들은 man 페이지로 살펴보기

### ANSI escape sequence
- ANSI
  - terminfo를 이용한 터미널 제어
  - 앞에 \e를 붙여서 사용
  - 색상 코드 사용
  - CSI(Control Sequence Introducer) 문자로 시작: \e, \033

- ANSI escape sequence list
  - CSI n;m [Hf]: Cursor position(row n, column m)
  - CSI n m: Select Graphic Rendition
  
- 색상  제어(Select Graphic Rendition)
  - CSI + n + m
  - n: 30 + color_number
  
- Practice: PS1(adv)
  - 프롬포트 앞부분 변경
  
# 19. shell function
### function
- 반복되는 호출 및 재사용을 위하여 function 작성
- function 부분을 별개로 file로 만드는 경우가 만음
- 이렇게 만들어진 file은 source 및 . 명령으로 import가 가능
- 모듈의 중요 설정은 환경 변수만으로 처리하는 것이 관습

### function examples(RHEL7)
- /etc/sysconfig/network-scripts/ifup
  - . /etc/init.d/functions: import하는 기능(source와 같은 명령)
  - &&로 결합된 명령 (optional하게 사용)
  - short circuit의 뒷 명령어가 복수의 명령어로 이뤄진 경우에는 brace로 묶어주는 경우가 있음
  
- function을 사용하기 위한 파일 작성법
  - my_module
  - my_info.sh
    - PREFIX_DIR을 미리 설정
    - PREFIX_DIR 변수가 없다면 특정 디폴트 디렉터리를 사용
      - . ${PRIFIX_DIR}/sh_mod/my_module
    - default 값을 PREFIX_DIR에 설정하도록 구성
      - source ${PRIFIX_DIR=${HOME}/bin/bash}/sh_mod/my_module 
  
- function: argument
  - 선언
    [function] func_name () {
      command...
    }
    
  - 실행
    func_name arg1 arg2 arg3 ...
    
  - 가변된 개수의 arg를 사용할 때는 loop 사용
    - shift n을 이용하여 n 만큼 인수리스트를 왼쪽으로 shift 시킬 수 있음
  
- practice: hello world
  - n개의 arguments를 처리하는 코드 작성법
  - 파일의 용량을 계산하는 스크립트 예제
    - stat 명령: file의 meta 정보
  
### Summary: Shell script 작성시 주의
1) exit code는 성공시 0, 실패시 non-zero(1~255) 값 리턴
  - function도 같은 원리

2) shell 변수들은 사용전 Null 검사를 꼭 하기
3) file에 접근하기 전에 유무, accessible 여부를 검사하기
  - -f: 존재 유뮤, -r: 읽기 여부, -w: 쓰기 여부, -x: 실행 여부

4) 인수(argument) 사용시 개수를 검사해야 함
  - script나 fucntion 실행 시 인수 개수($#)를 검사하는 과정 필요

5) 성능을 위해 Built-in command를 우선적으로 사용
  - expr 수치 연산 보다 let, (())를 사용하는 것이 좋음

### practice: ssh: conn. remote server
- ssh로 원격 서버의 명렁어 실행
  - 원격 실행 시 password를 쓰지 않기 위해 key를 사용
  - ssh-keygen을 사용해서 키를 생성(~/.ssh에 생성)
  - ssh-copy-id로 키를 복사
  
- rsshexec1.sh
  - 서버에 접속해서 서버에 명령어를 전달
  - 파일을 실행할 대 vmstat 명령어 전달
  
- rsshexec2.sh
  - srv list 하나 더 늘림
  - loop 부분 접속한다음에 loadavg(부하 평균중에 1번 수치) 가져오고 그 값이 0.8보다 크면 -a를 이용해서 보여줌
  - 소수점 계산을 위해서 bc 사용
  - 무한 루프로 실행하기
  
- rsshexec3.sh
  - 함수 형태로 써주기
  
### Practice: function
- /etc/profile.d/bash.sh에 ? () {ech0 "$*" | bc -l; } 코드가 들어있다면 계산기로 사용하는 것
- 16 진수로 계산하는 것이 귀찮으므로 함수 작성

### ssh
- ssh-keygen, ssh-copy-id를 이용해서 키 기반 접속 세팅
- 특정경로에 가서 특정 파일을 묶어서 백업받기
- ssh를 이용해서 복수의 서버로부터 파일을 백업받으려면 순차 처리이므로 느림(parallel을 사용해서 병렬처리 가능)
- function: pathmunge
  - path 경로를 추가할 때 사용, PATH를 중복해서 등록하지 않도록 사용
  - 이 함수를 응용해서 개발자들은 LD_LIBRARY_PATH에 경로를 추가하는 기능을 만드는 경우도 많음
  
