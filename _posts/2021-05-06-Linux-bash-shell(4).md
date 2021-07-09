---
title: "리눅스 bash 기초(4)"
excerpt: ""

categories:
  - Linux
tags:
  - Linux
  - UNIX
  - bash
  - bash shell
---
# UNIX commands
### UNIX commands and shell script
- shell script를 잘 작성하려면 문법이나 exit code를 처리하는 것도 중요하지만 명령어들의 조합도 관건
- 명령어의 종류만 알고 man page를 보고 사용하면 좋음
- 간단한 사용법과 어떤 명령이 있는지 알아두기

### builtin/external cmd
- 내부 명령(builtin cmd) 
  - fork의 부담이 없음
  
- external cmd
  - 무조건 fork-exec 과정을 거치므로 overhead가 존재
  
- 같은 기능이라면 built-in cmd가 유리(expr보다 let이나 ((...)))
- UNIX 명령어는 일반적으로 shell에서 사용하기 쉽도록 최소한의 출력만 하기
- 스크립트가 아닌 human readability는 verbose 옵션으로 사용하기(-v)

- 많이 사용되는 명령어
  - 정렬 출력
    - **sort**: 라인 단위 정렬
      - -k: 키 필드 지정(F[.C][OPTS][,F[.C][OPTS]] 지정 가능)
        - F: field number(column number)
        - C: offset position
        - OPTS: sort option(bgfgiMhnRrV)
          - k 7n -k 5nr: 7번째 필드로 sort, 7번째 필드가 겹치면 5번째 필드 reverse
      
      - -t: 구분자 지정(default=blank)
      - -u: unique(중복 제거)
      - sort: practice
        - sort -M -k 6 -k 7n -k 5nr: 6번째의 월, 7번째의 일, 5번째 역순으로 sort
        - sort -k 1.8,1.18n contdata.txt: offset 단위로 나누기, 1번째 필드 8번째부터 18번째까지 필드를 잘라서 sort

    - head, tail: 스크립트 앞 부분, 뒷 부분 출력
      - head: 스트림의 첫 부분만 출력(기본 10라인)
      - tail: 스트림의 끝 부분만 출력
        - f: 추가되는 스트림을 계속 출력(로그파일 분석시 사용)
      
      - cat: 앞에서부터 출력, tac: 뒤로 뿌리는 것
      
    - expand: 탭을 스페이스로 변환(built in)
    - paste: 열 단위 합치기
      - s: serial option

    - tee: 출력과 동시에 파일 저장
    - pr: print text file
      - 예전에는 text file을 print로 보냈기 때문에 text file을 적합한 규격으로 만들어 주는 것
      - -w: width, -l: line
      - 바로 화면에 출력하는 용도로도 쓰고, 파일로 다시 한 번 받아서 다른 터미널 환경을 넣을 때 사용하기도 함
  
  - 데이터 조작: 기능이 더 많이 필요하면 awk로 해결
    - join, split: 키 값을 기준으로 열 단위 합치기, 파일 나누기
      - join: 공통키를 가지고 열단위 합치기(컬럼 지정에 자주 사용), sort와 같이 사용
        - 원시적 sql 기능을 함
        - 1 #: 1번 파일의 키 필드 위치
        - 0 " ... ": 출력 포맷

      - split: 처리할 데이터가 큰 경우 이를 자르는 용도
      - Practice: split
        - split -d -a 3 -l 100000 /usr/share/dict/words: 100000 line 단위로 자르기
        - --additional-suffix: split 되는 것에서 특정 확장자 추가
  
    - **cut**: 오프셋, 열 단위로 잘라내기(데이터가 복잡하면 awk 사용하기)
      - -d <delimeter>
      - -f <field ...>
      
    - fold: width 단위로 라인 자르기
    - seq: 시퀀스 생성기(brace 기능으로 대체 {1..50})
      - s: separator 지정
      
    - **read**: 데이터 입력 읽어들이기(built in)
      - -d delim: delimeter
      - -n nchar: 지정된 크기만큼 읽기
      - -N nchar: 지정된 크기만큼 읽기
      - -s: slient mode
      - -t timeout: 소수점 가능
        - read -t # -N 0: sleep 모드(SIGTTIN이 발생하므로 백그라운드로 작동시킬 수 없음)
        
      - -a arrayname: 배열로 저장
      - -u fd: 지정된 fd에서 읽기
      - IFS 변수를 구분자로 사용(default IFS: whitespace, tab, new line)
      - /bin/bahs 대신 /bin/sh를 사용하면 bash의 제한된 호환 모드로 작동하기 때문에 위험
  
  - 실행 검사
    - test: 조건 테스트
    - sum, md5sum: 체크섬
    - wc: 워드 카운트(라인, 단어, 바이트)
    - **stat**: 파일 정보 읽어오기
    - **eval**: 구문 실행(built in)
      - 문자열을 명령어로 실행
      - 변수명이 동적인 경우 유용함
      - key & value list를 만들 때 사용
      - 외부 명령어의 실행 결과로 script가 생성된 경우 사용
      - eval: practice
      - 어떤 값을 구해야하는데 따옴표가 들어있어서 변수의 값에서 따옴표를 날리고 싶으면 data만 있는 형태로 뽑아내고 싶을 때 eval 사용
      - eval: print bash script
        - 외부 스크립트나 프로그램의 실행 결과가 실행을 하면 프로그램이 다시 bash shell code를 뱉어내는 경우가 있음
        - 어떤 프로그램의 실행 결과로 shell의 환경 변수를 세팅해주는 프로그램이 있는 경우가 있을 때 eval 사용(보안프로그램, 코덱, 라이선스 관리자)\
      
    - **exec**: 실행 이미지 교체, 파일 열기(built in)
      - 실행: 프로세스 이미지 교체
        - static resource: PID, fd, sigmask 등이 유지

      - 파일 열기
        - 특정 파일 기술자(file descriptor)로 오픈
        - 현재 쉘에서 파일을 읽거나 쓰거나 닫을 수 있음(서브셀을 만들지 않음) 
        - exec #<FILE: 읽기용 FILE 오픈(오픈된 파일은 파일기술자 #번을 할당받음, 생략 시 0)
        - exec #>FILE: 쓰기용 FILE 오픈
        - exec #>>FILE: 추가용 FILE로 오픈하는 방식
        - exec #<&-: #번 파일 기술자 닫음(읽기채널)
        - exec #>&-: #번 파일 기술자 닫음(쓰기채널)
        - exec #<>FILE: 파일을 RW가 가능하도록 오픈
        - file descriptor는 child process에 상속
          
      - exec: practice1
      - exec: practice2
        - 자식 process를 수행하면 변수가 상속되고 자식 process가 종료되면 변수도 종료됨
        - exec 로 할당해서 변수 선언해서 현재 쉘에서 실행하도록 해결
        - sh로 실행하면 오류(bash로 실행하기)
  
      - exec: practice3
        - dup/redirection 사용하기
          > $ exec 3>&1
          > $ exec 1> >(paste /dev/null -)

      - exec: socket
        - 파일을 열 때 socket을 열수도 있음
        - exec 3<>/dev/tcp/www.vim.org/80
        - tcp 대신 udp를 사용하려면 /dev/udp/host/port로 호출
  
  - 평가 포매팅
    - expr: 표현식 실행(산술, 패턴, 문자열 조작)
    - **let**: 산술 계산(built in)
    - **echo**: 출력
    - **printf**: 형식화된 출력
      - C-style precision, conversion
      - %s를 사용할 때는 인수의 개수에 주의
        - $(...)를 double quotation mark로 감싸주면 여러 개의 인수가 1개의 string으로 인식
  
  - 작업 컨트롤
    - fg, bg: 포그라운드, 백그라운드(built in)
    - jobs: 작업 리스트 확인(built in)
    - wait: 서브쉘 종료 대기(built in)
      - wait child process
      - 여태까지는 순차적으로 작업 진행하는데 비효율적이기 때문에 multi-tasking 지원
      - return 값을 받지 않아도 되면 &으로 작업을 돌리고 wait로 막아둠(이렇게 하는 경우는 많지 않음)
      - 작업이 잘 작동했는지 성공 실패를 테스트 하는 부분으로 사용이 많음
  
  - 기타
    - **date**: 날짜 출력 
    - **time**: CPU 시간 측정(built in과 external cmd 모두 있음)
    - sleep, usleep: 초 단위 슬립, 마이크로 초단위 슬립
    - getopt: 옵션 리스트 처리(built in)
    - mkfifo: named PIPE 생성
      - C-style의 fifo와 동일
    
    - **xargs**: stdin으로부터 argv를 받아서 명령어 생성
      - command | xargs build-cmd 방식으로 command를 넘겨주는 형식으로 자주 사용
      - option
        - d <delimeter>
        - i <replace-str>: default replace-str = {}
          - {}를 만났을 때 앞에서 |로 받은 것을 {} 안에 넣어줌
        - t: verbose
  
  - 계산기
    - bc: 실수, 승수, 스퀘어 루트, 삼각함수, 로그, 지수, 미분, 진법 계산 가능
      - bc: practice
      - bc: function
      - -l 옵션: math emulation mode(C로 짜여져서 속도가 빠름)
        - s(x): sin
        - c(x): cosin
        - a(x): arctangent
        - l(x): 자연로그
        - e(x): 지수 함수
        - j(n, x): 베셀 함수
      
    - dc: reverse polish calculator
  
  - 터미널
    - tput: 터미널 제어(ANSI)
  
  - Network
    - **ssh, scp**: secure shell, SSH 프로토콜을 이용한 원격 명령어 및 파일 전송을 실행(scp는 미래에 sftp에 의해 deprecated 되니 지양)
    - **nc, curl, wget**: 원격 네트워크 접속 및 전송

### Transient Process
- Tansient Process
  - 임시로 사용되는 프로세스
  - 임시로 사용되는 1회성 서비스
  - deamon service로 만들기에 무겁거나, 부팅시 자동 실행하는 서비스가 아니거나, 반복 사용을 하지 않거나, 개발 단계이거나, 귀찮을 때 사용

- commands
  - nohup
    - 아주 오래된 명령어
    - SIGHUP를 막는다는 의미에서 nohup
    - Session이 파괴되면, 그 결과 session에 속한 child process들이게 SIGHUP이 전달되는데 그것을 막음
    - nohup 자체가 signal mask를 걸고 blocking 해줌(상속이 일어나면서 자연스럽게 blocking)
    - systemd를 사용하지 않을 때에만 사용

  - systemd-run
    - nohub의 문제점
      - 프로세스로만 관리되고, 서비스로는 관리되지 않음(나중에 죽이거나 message를 뱉어내는 것을 확인하기 힘듬)
      - log 관리가 안됨
      - resource limit을 적용하기 까다로움
  
- nohup
  - nohup <command>
  - background running 상태로 만들어서 사용
  - SIGTTIN, SIGTTOU을 막기 위해 stdin을 /dev/null로 redirection, stdout과 stderr는 $HOME/nohup.out으로 redirection
  - tmux 나 screen에서 실행하는 경우도 있지만 많이 사용하지 않음
  - practice: nohup
  
- systemd-run
  - bash shell script를 실행하는 방식은 더 이상 사용하지 않음
  - 1회성 임시 서비스(systemd-run 사용)
  - 등록 서비스(systemd용 unit 파일 작성)
  
- systemd-run 사용하기
  - systemd-run --unit=name --scope --slice=slice_name command
    - unit을 지정해주지 않으면 random하게 생성
    - scope를 적으면 foreground에서 실행(external command 처럼 작동), 적지 않으면 background 실행
    - slice를 적으면 slice_name으로 구별 가능한 name이 만들어짐
  
  - practice: systemd-run
    - 되도록이면 상대 경로보다 절대 경로 사용해주기
    - 일반 unit과 다르게 서비스를 stop 시키면 사라짐(상태는 없다고 나오고, 다시 실행 불가)
    - 다시 실행시키고 싶으면 systemd-run을 다시 실행
  
- slice
  - systemctl -t slice
  - 자원에 제약을 위해서 slice 작업 가능
  - slice 새로 만들기와 기존 slice 추가 가능
  
- properties(자원에 제약걸기)
  - systemd-run updatedb: 파일 system 관련 db 만들기(목록 db)
  - systemd-run -p IOWeight=10 updatedb: 리소스 제약
  - systemd-run --on-active=30 --timer-property=AccuracySec=100ms updatedb: timer 제약
  
- systemd.property: cgroup
  - CPU
    - CPUWeight=100: default(100), 범위는 1~10000, 높을 수록 slice time을 더 많이 받음
    - CPUQuota=50%: cpu 할당을 %로 받을 수 있음
  
  - Memory
    - MemoryLimit=value: bytes 단위로 메모리 제한
    - MemoryMin=10M: 10M까지 release 대상이 되지 않고 보호
    - MeomoryHigh=2G: 느슨한 high limit(이 값을 넘을 수 있지만 적극적으로 reclaim의 대상)
    - MeomoryLow=20: 느슨한 low limit
  
  - IO
    - IOWeight=200: default(100), 범위 1~10000, 높을 수록 더 많은 I/O
    
- systemd-run으로 생성된 transient unit의 journal 보기
  - &#35; journalctl -f -u highdirty
  
### Summary
- systemd-run은 nohup을 대체
  - service 혹은 scope로 실행 가능(scope 실행시 tmux와 같이 사용)
  
- systemd의 기능이 resource control 기능(cgroup)을 통합 관리

# System stat
### Load Average
- 시스템에 걸리는 부하의 평균 수치
- 부하 평균, 로드 평균, LA, loadavg로 줄여서 부르기도 함
- LA는 1분, 5분, 15분 3가지 수치가 있음
- 절대적 수치보다 추세를 봐서 Process life-cycle에 대해서 이해해야함

### Process Life cycle
- create 단계: 셸 스크립트를 작성한던지 ls 같은 명령에서 실행하던지 fork를 통해서 프로세스가 생성하는데 이 프로세스의 생성 
- ready 상태(runnable): 프로세스가 생성되면 CPU를 할당받기 위해서 일할 준비가 되어있는 상태
- scheduling(dispatch): ready 상태가 되겠다고 하고 운영체제의 스케줄러가 실제 CPU를 할당해주기 위해서 스케쥴링 하는 과정
- running 상태: CPU를 실제 할당받아서 실행
  - preemption: running 상태에서 CPU를 과다하게 소모해서 운영체제가 뺏어가는 것
  - yield: 자발적으로 cpu를 처리하지 않고 있으니까 다른 것을 먼저 처리해달라고 하는 것(프로그래머가 소스 코드에 집어넣음)

- blocked 상태: running 상태에서 CPU는 굉장히 빠르기 때문에 일을 하고서 기다려 줄 수 없음(I/O event, 메모리 관리 작업, signal 작업)
- wakeup: IO 작업을 완료한 후 다시 깨어나는 단계(다시 ready 상태로 돌아가게됨)
- 화면에 출력 혹은 파일에 기록을 최소화해야 성능이 빨라짐
- 그래서 verbose 옵션을 실무에서 자주 사용하지 않음
- terminated 단계: 프로세스가 하던 일을 종료(exit code가 나옴)
- 좀비 프로세스(defunct 상태)는 terminated 단계

### Load Average History
- 표준적인 LA 산출 방법은 없음(OS 마다 다름)
- 하지만 정의는 존재
- 초기 UNIX에서는 일반적으로 "작업 대기큐(CPU 수요)"의 길이로 정의
- 작업 대기큐: 대기 상태와 running 상태를 합친 것의 의미
- 1분 loadavg는 1분 동안 프로세스들의 CPU 수요 평균값 의미
  - 1분 동안 온전히 CPU를 독점했다면 1.0의 값을 가짐
  
- 리눅스의 경우 초창기 UNIX와 비슷함
- 1993년 IO 작업(uninterruptable task)를 포함시킴
- uninterruptable task가 포함되어 과도한 I/O 처리(socket, FS)로 인한 시스템 응답이 늦어지는 것이 loadavg 반영됨

### Linux에서 Loadavg의 의미
- CPU 수요(Running + Runnable) + IOwait(uninterruptable task)
  - NFS나 web proxy의 경우 일반적으로 높은 loadavg를 가질 수 있음
  - IOwait만으로 LA를 평가할 수 없고 IOWait이 높은 시스템은 CPU를 제대로 활용하지 못할 수 있다는 뜻도 됨
  
- 지수 이동 평균을 사용하여 계산함

- CPU의 개수에 따라 같은 수치라도 다른 영향을 받음
  - "Loadavg > CPU 개수"인 경우
    - CPU 할당을 원하지만 CPU를 할당 받기 위해 waiting이 있다는 의미(대기되는 프로세스의 개수가 많은 자체보다는 부수적 발생가능한 latency나 memory 부족이 문제)
    - 하지만 IOwait이 높다면 IOwait이 높아서 loadavg가 상승하는 경우는 다른 의미의 부하
  
### OS의 overhead 영향을 주는 요소들
- Process life-cycle에서 Running, Ready 상태가 빠르게 스위칭되는 경우 → scheduling cost의 증가(LA 증가)
- Process life-cycle에서 Blocking 시간이 긴 경우 → CPU를 활용하지 못함(dirty page를 늘려줘서 해결)
- Load average의 급격한 증가가 확인되면 계속 시스템에 부하가 증가, CPU에서 IOwait가 있었는지 확인해보기

- 다양한 툴이 주는 정보
  - Load Average: uptime, /proc/loadavg
  - Process status: ps, pstree
  - Process table: top, htop, atop
  - Process time: time, /usr/bin/time
  - Process map: amap
  
- 정보를 주는 것이 미묘하게 다르기 때문에 다양한 툴 제공
- tool 명령어나 option 보다 이론적 배경이 더 중요(데이터가 가진 의미를 이해해야 응용이 가능해짐)

### Load Average 읽기
- uptime: 정보가 나오는데 load average 평균이 나옴(1분, 5분, 15분 순으로)
- sar -q: 시간대 별로 LA가 어느정도였는지 기록해줌(유용하게 사용)
- 1.72 1.23 0.45: 시스템 부하가 점점 늘어나고 있음
- 6.15 2.21 2.10: 최근 1분에 갑작스럽게 부하가 늘어남
- 2.89 3.58 4.25: 부하가 점점 줄어들고 있음

- LA를 표시하는 명령어
  - uptime
    - monotonic timer: 시스템이 부팅했을 때 시간부터 얼마나 지났는지
    - 현재 유저수: login된 세션의 개수
    - load average: 1분, 5분, 15분
    
  - top
  - sar -q(history)
  - /proc/loadavg 직접 읽기
    - 5개의 필드(앞에 3개가 1분, 5분, 15분)
    - running/total
    - last pid: PID가 빠르게 중가하는 경우, 너무 많은 fork가 발생하는 것을 추정(fork-bomb)
    - ps에서 -L을 쓰면 스레드까지 포함
    - Practice: cpu time
    - Practice: IO time
  
### vmstat
- 예전에는 virtual memory를 알려줬는데 부수적인 기능이 많아져서 vmstat만으로 기본적인 체크 가능
  - process queue, memory, swap, block io, cpu
  - active/inactive memory
  - fork
  - statistics
  - disk
  - slabinfo
  
- vmstat: 6개의 정보를 알려줌(일반 유저도 가능)
  - -S: size factor(kKmM)
  - -a: active memory(LRU에서 가장 최근 사용 메모리), inactive memory(가장 오래된 메모리)
  - -d: disk
  - -m: slab 메모리 확인하기(inode, tcp 등등 커널에서 사용하는 메모리의 어떤 부분이 얼마만큼 사용하는지 확인하면 OS 트러블의 원인을 알 수 있음)

  - procs: process 관련 대기 큐 보여줌
    - r: runnable + running(CPU 사용)
    - b: uninterruptible sleep(I/O)
    
  - memory 
    - swpd: virtual memory가 사용된 부분
    - free: free memory(사용 가능한 메모리를 말하는 것이 아니라 오히려 사용된 적이 없는 메모리)
    - buff: buffer 메모리(디스크에 원본이 있는 것)
    - cache: cache 메모리(디스크에 원본이 없는 것), page cache(디스크에 사본이 있는 것)
    
  - swap
    - si: swap in
    - so: swap out(메모리 부족)
    - 가용메모리보다 더 큰 공간을 사용할 수 있게 해주는 기능(Windows에서는 page)    
    - swap이 발생하면 시스템의 메인 메모리가 부족해서 디스크와 교환 작업이 발생
  
  - io
    - bi: block in(read)
    - bo: block out(write)
    - 디스크 장치는 block device
    
  - system
    - in: interrupt
    - cs: context switches(많이 발생하면 CPU가 실제 일보다 스케줄링으로 일을 나눠주는 게 많음)
    
  - **cpu**: 
    - us: CPU가 kernel code가 아닌 곳에서 작동한 Time
    - **sy**: 커널에서 소모한 시간(프로세스를 만들거나 디스크에 기록, 메모리 할당, 네트워크 등 시스템의 장치에 관련된 기능을 할 때)
    - id: CPU가 노는 시간
    - **wa**: IOwait(IOwait가 늘어나면 IO에서 병목이 발생해서 실제 CPU가 동작을 못해서 효율이 떨어진다는 것을 의미)
    - st: virtual machine에서 뺏어간 시간
  
- fork
  -f: fork 횟수 보기 (fork-bomb)
  
- statistics
  - vmstat -s
  - 다양한 정보를 모아주기 때문에 여러 툴로부터 정보를 모으기보다 vmstat -s 하나로 분석하는게 쉬울 수 있음
  - 10~30초 간격으로 2~3번 이상 출력 결과가 필요한 경우도 많음
  - idle cpu ticks가 빠르게 늘어나면 CPU가 놀고있음
  - idle cpu ticks가 잘 늘어나지 않으면 CPU 과부하 상태
  - IOwait cpu ticks가 빠르게 늘어나고 잇으면 I/O 병목
  - 여기에도 forks 횟수 볼 수 있음
  
### Summary
  - Memory 중요 부분
    - active/ inactive memory
    - used swap
  
  - CPU ticks 중요 부분
    - user cpu ticks가 높은 경우
    - system cpu ticks가 높은 경우
    - idle cpu ticks
    - IOwait cpu ticks가 높은 경우
  
  - page in/out, swapped in/out
    - swapped in/out이 발생한다는 것의 의미
  
  - forks
    - forks 횟수가 과도하게 늘어나는 것은 fork-bomb 의심
  
# top
### top
- Top Process, Table of process 등 각종 지표를 모아주는 기능이 있음
- 많은 유저들이 기본적으로 사용하지만, 잘 다루지 못한 유틸리티로 유명함
- top, ps, vmstat을 얼마다 잘 다루는지에 따라 실력이 달라짐
- procp 구버전과 props-ng 신 버전이 존재(신버전에는 filter 기능이 있음)
- 유틸 중 새로 작성된 것들은 뒤에 ng suffix가 붙음
- online man page가 매우 잘되어있음

### top 사용시 주의점
- multi-process의 level/depth(자식의 자식)가 높은 경우에는 부하를 줄 수 있음
- 프로세스의 개수가 많다는 것은 감시하기 위해서 수집과 정렬에 CPU 부하가 상당할 수 있기 때문
- 수천개의 fork level이나 multi-depth를 가진 경우는 애초에 시스템에 부하를 줄 수 밖에 없는 나쁜 구조

### top
- top의 가장 윗 줄: 부팅 시간, 접속한 유저 수, 로드 평균 값(uptime명)
  - toggle key: l(정보 숨기기)
  - refresh: spacebar(기본적으로 3초마다 업데이트되는데 즉시 업데이트 되기)
  - quit: q(나가기)
  
- 2행
  - total process
  - running: 작동 중 프로세스 개수
  - sleep: 블록 상태인 프로세스 개수
  - stopped: 중단된 프로세스 개수 (프로세스를 할당닫지 않도록 정지된 상태, SIGTSTP(^Z)가 발생했을 때)
  - zombie: 좀피 프로세스 개수(=defunct)
  - toggle key: t
  - Thread toggle: H
  
- 3행: CPU 사용량
  - us: user(단순 영역)
  - sy: system(kernel 영역)
  - ni: niced user(우선순위 높은 user)
  - id: idle(idle이 높으면 CPU가 널널)
  - wa: I/O wait(IO 병목 현상)
  - hi: H/W interrupt
  - si: S/W interrupt(네트워크를 사용하면 자연스럽게 높아짐)
  - st: stolen cpu time(VM)
  - toggle key: 1 (one)
  
- 4~5행: Memory(free 명령어의 실행결과와 같음)
  - free: 메모리는 할당되지 않은 것, avail Mem: 사용 가능한 메모리(free Mem+buff)
  - Mem: physical memory
  - Swap: swap area(disk or file)
  - buffers: file system meta data
  - cached: pages with actual contents or block devices
  - toggle key: m
  - Extended-memory-scale: KiB → MiB → GiB → TiB → PiB → EiB
    - Summary area: E(대문자의 스케일이 올라감)
    - Task area: e(소문자의 스케일이 올라감)
  
### Top: tesk area
- zero-suppress toggle: 0
  - zero-suppress를 켜면 수치가 0인 필드를 숨겨서 쉽게 판독할 수 있음

- toggle idle process: 0
  - idle-process를 토글하면 실제 CPU를 사용중인 프로세스만 빠르게 찾아낼 수 있음
  - idle을 켜놓고 깜빡하면 오랫동안 sleep된 프로세스를 찾아내지 못하는 경우가 있어서 memory 관련 수치를 확인할 때는 idle을 off로 해둠

- L: find, &: again
  - L을 입력하고 gnome(or bash)를 검색해보고 &를 입력해서 다음 아이템을 찾아보기
  - Home키를 누르면 첫 행으로 이동

- c: toggle COMMAND column format(full command line)
  - COMMAND 칼럼의 출력 포맷을 토글 시킬 수 있음

- V: toggle forest view
  - 출력 포맷을 계층 구조로 토글 가능
  
### Top을 사용하는데 도움을 주는 강조 기능
- b (bold/reverse mode) toggle
  - x: toggle highlighting(sort field): 수평 부분 빨리 찾아줌
    - fields management에서 설정 가능
    - 바탕화면에서 <, >으로 필드 이동 가능
    - R: reverse sorting 토글
     
  - y: toggle highlighting(running task): 수직 부분 빨리 찾아줌
  - b+x+y: bold + 수평 + 수직

- z (color/mono mode) toggle

- Z: color mapping menu
  - target 설정하기
    - S: Summary Data
    - M: Messages/Prompts
    - H: Column Heads
    - T: Task Information

  - color 설정하기
    - 0: black
    - 1: red
    - 2: green
    - 3: yellow
    - 4: blue
    - 5: magenta
    - 6: cyan
    - 7: white

  - 끝내기
    - <Enter>: commit and
    - q: abort
  
- h, ?: help 기능

### field management
- 사용할 수 있는 filed는 top 버전에 따라서 다름
- * 표시가 있으면 화면에 보여지는 것(d, space를 누르면 보여지지 않음)
- Right: Select, Enter or Left: commits
- 중요한 필드
  - CPU
    - **%CPU**
      - CPU 사용량 %: multi-thread의 경우는 합산
      - Irix mode off: CPU의 개수를 나눠서 계산
      - Irix mode on: CPU의 개수를 나누지 않음(사용하기: I)

    - **TIME**: CPU TIME (HH:MM:SS) 누적시간
    - TIME+: CPU TIME, hundredths (HH:MM:SS.ss) 누적시간

  - Memory
    - **%MEM**: 메모리 사용량
    - VIRT: 가상 메모리 크기 (실제 할당된 크기 아님)
    - **RES**: 실제 할당된 크기(RSS라는 단어를 더 많이 사용), RAM에 있는 사용량(공유 영역 포함)
    - SHR: 공유 영역 메모리 크기
    - **SWAP**: 스왑 메모리 크기
    - **DATA**: 데이터 영역의 크기(Data + Stack, 실제 할당된 크기가 아님)
    - CODE: 코드 영역 크기(실행 코드)
    - USED: RES + SWAP
    - **nMaj**: Major page faults(DISK I/O) =hard page faults
    - mMin: Minor page faults =soft page faults
    - vMj: Major faults delta(DISK I/O)
    - vMn: Minor fault delta

  - Process
    - PID
    - PPID
    - **S**: Status
      - D, I, R, S, T, t, Z

    - **WCHAN**: Wait channel, 슬립 상태에서 머무르는 경우의 코드 루틴(함수)
  
### Runtime Configuration file
- 커스텀마이징을 하면 설정을 저장해줘야 함
- 실행 중 W 키를 누르면 현재 설정 저장
  - old version: ~/.toprc
  - new version: ~/.config/procps/toprc

- toprc를 vim으로 편집할 대는 fenc가 latin1으로 되어있어야 안깨짐
  
### top 실행 CLI options
- -a: sort by memory usage
- -b: batch mode(1회성 실행)
  - -n #: 반복 횟수 지정
- -d ss.tt: sec.tenths interval
- -H: threads toggle
- -i: idle processes toggle
- -p: monitor pids(-pN1 -pN2 ...)

### Practice(advanced) 실무형 예시
- SysWide 1: %CPU 사용량이 높은가
- SysWide 2: Mem/Swap 사용량이 높은가
  
### Filter
- 신버전에서만 지원
- 특정 조건을 걸어놓고 조건에 대해서만 출력
- Filter 설정
  - o: add filter(ignore case)
  - O: add filter(case sensitive)
  - ^O: show current filters
  - =: reset filter in current window
  - +: reset filter in all windows

- Filter 적용시 유용한 기능
  - J: justify-numeric-columns toggle
  - e: extended-memory-scale in task windows(print scale)
  
### htop, atop
- htop
  - MS-DOS의 노턴 유틸리티 방식으로 만듬
  - forest view 지원
  
- atop
  - top의 기능 뿐만 아니라 디스크, 네트워크의 기능 모두 모아놓음
  
# strace(system call tracing)
### system call
- 시스템 호출
- 시스템을 호출해서 일을 시키기 위해서 사용
- 파일 저장, 메모리 확보, 네트워크 통신 등이 system call로 이루어짐
- system call이 제대로 작동하지 않을 때에는 여러 가지 원인이 있기 때문에 system call을 추적해야 함

### system call을 추적하는 것
- Linux를 migration한 뒤에 특정 process가 제대로 작동하지 않는 경우에 유용
  - 내가 작성한 프로그램은 구글링에 나오지 않음
  
- 어떤 process가 출력하는 값이 어떤 파일에서 가져오는지 알고 싶을 때 유용
- 어떤 process가 잘 작동하다가 멈출 때 도대체 어디서 멈추는 것인지 알고 싶은 경우 유용
- 이럴 때 사용하는 것이 tracing 기법
- tracing tool 중에 가장 기초적인 것이 strace

### strace 주의점
- strace는 모든 system call을 추적하기 때문에 어느 정도 시스템에 부하를 줌
- 화면이나 파일에 출력을 하면 IO가 발생하기 때문에 부하가 더 커짐
  - 정확한 인수값을 확인하기 위해서 출력 string size를 지정하는 것이 필수적(default는 32, 보통 128이나 256을 많이 사용)
  - 출력 사이즈가 클수록 IO가 늘어나므로 화면 출력보다는 파일로 저장하는 것이 더 좋음
  
- strace를 사용할 때는 되도록이면 짧은 시간으로 추적해야 함(부하가 걸리기 때문에)
  - 연달아 추적할 필요가 있다면 횟수를 조절하면서 반복하는 것이 좋음
  
- 추적 대상이 되는 함수를 명확하게 기술하는 것도 필요함
  - 정확한 표현이 힘들다면 변경해가면서 추적하는 연습
  
### strace start
1. strace <command>
  - strace가 command를 fork-exec로 추적하는 방법
  - 단발성 명령어를 추적할 때 사용
  - Practice: strace
    - execve
      - 새로운 명령어가 시작하면 fork가 실행되고 exec 작업이 발동
      - fork는 이전 작업이므로 catch가 안되고 fork 이후 exec 계열 함수로 이미지가 교체된 것을 의미

    - access
      - 파일이 존재하는지와 UNIX mode(rwx) 값을 확인
      - 실패하면 -1을 return하고, 전역 변수인 errno에 에러코드인 "E..." 형식을 리턴(자세한 것은 man 2 access로 확인하기)
      
    - openat
      - 파일을 오픈하는 행위
      - 해당 파일이 존재하고 오픈에 성공하면 양수의 file descriptor를 리턴
      - 실패시 -1을 리턴하고, 전역 변수인 errno에 에러코드인 "E..." 형식을 리턴(자세한 것은 man 2 access로 확인하기
  
    - close
      - open, close는 항상 pair로 되어있는데, 만일 close되지 않는 fd는 계속 열어두고 써야하는 경우 실수로 close 되지 않게 제한된 리미트 값에 도달하여 에러가 발생할 수 있음
      - 만일 open시 return 값이 -1dlrh errno 에러 코드가 ENFILE로 설정되면 ulimit -n의 총합을 늘려주거나, 근본적인 수치인 PAM /etc/security/limits.conf를 조정할 필요가 있음  
  
    - read
      - read(...) system call 행 분석: read(2) 매뉴얼 페이지와 같이 보기

2. strace -p <pid>
  - Running process를 추적하는 방법
  - daemon 서비스로 작동하는 경우 추적
  - 누구의 권한으로 시작하는지 중요하기 때문에 보통 root 권한으로 사용

### error, exit code
- strace로 추적할 때 특정 함수의 exit code, 즉 리턴값을 중요하게 봐야함
- errno는 errno.h 값을 참조
- practice: strace cat

### option
- -s strsize: 사이즈를 늘려줌(default는 32)
  - practice: strsize

- output
  - starce ls -al을 실행한다면 어떻게 출력?
    - stdout: 추적할 프로세스의 출력(ls -al 결과 출력)
    - stderr: strace의 추적 정보(strace의 결과 출력)
    - -o output_filename: redirection을 쓰는 방법보다 훨씬 간단(실무에서 위에 방법은 거의 안쓰고 이 방법만 사용)
  
- decoding
  - -y: 실제 경로를 알려줌  
  - -yy: 실제 경로를 알려줌(더 많은 정보로)
  - Practice: decode
  - Practice: decode fd(socket)
  
- count time
  - 특정 sysem call이 불려서 걸린 시간, 몇 번 불렀는지, 에러는 몇 번 났는지 알려줌
  - -c
  - -C
  - Practice: time, calls
  - Practice: time, calls, errors
  
- path
  - -P path
  - 특정 path에 대해서 system call이 발생할 때만 필터링
  - 특정 파일을 건들일 때만 출력
  
- expression
  - -e expression
  - expression: [qualifier=][!]value1[,value2]...
    - qualifier: trace, abbrev, verbose, raw, signal, read or write
    - !: not
  
  - -e trace=syscall_set
    - file, process, network, signal, ipc, desc, memory
    - -c, -C 옵션과 같이 사용해서 calls, error를 파악하기 좋음
  
  - -e <read|write|signal|status>=set
    - read=set: read=3,5: fd 3번, fd 5번일 때 훔쳐보기
      - Practice: read(read를 이용해서 socket 통신 시 fd의 데이터를 훔쳐보기)
      
    - write=set: write=3,5: fd 3번과 5번에 쓰기가 발생했을 때 훔쳐보기
    - signal=set: signal이 발생했을 때 signal 보기
    - status: return 값 확인
      - successful, **failed**, unfinished, unavailable, detached
  
- 5.x: 버전이 조금 다름

### tacking fork
- f: : 따라가서 화면을 똑같이 보여줌
  - strace -f <command>: 1회성 
  - stracd -fp <pid>: daemon service로 작동하는 경우(이미 작동하는 경우)

- ff: output을 분리해서 각각의 파일로 저장(--follow-forks --output-separately)
- Practice: child process
- Practice: child processes

### time
- -r: relative time
- -t: time
- -tt: microsecond
- -ttt: UNIX time stamp + microsecond
- -T: time spent in system call
- Practice: pref. tracing
  - awk로 실제 실행 시간이 가장 높은 부분이 추출함
  
### ANSI color
- strace의 -e trace와 -T -r을 같이 사용하면 특정 이벤트만 트레이싱하면서 확인이 가능해짐
