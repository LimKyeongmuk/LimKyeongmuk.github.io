---
title: "리눅스 bash 기초(5)"
excerpt: ""

categories:
  - Linux
tags:
  - Linux
  - UNIX
  - bash
  - bash shell
---
# Memory
### System Memory
- 모든 Modern OS의 Meomry라고 불리는 것
- Virtual memory를 의미(vm, 요즘은 vm이 virtual 머신라고도 불려짐)
- Physical memory + Disk area (paging)
- 메모리는 장비에서는 RAM이지만 보통 가상 메모리를 말함
- 메모리는 Paging과 Segmentation으로 이루어짐

### Page vs Segment
- Page
  - 규격화(균일 사이즈)된 메모리
  - 규격화된 크기로 잘라야 분철이나 인덱싱이 편함
  - OS가 바라보는 메모리 규격
  
- Segment
  - 비규격화(균일하지 않은 사이즈)된 메모리
  - page를 가져다가 다시 자르고, 붙여서 만듬(여기서 fragment 발생)
  - 프로세스가 바라보는 메모리의 규격
  
### Paging
- page를 가지고 작업하는 것
- CPU의 MMU라는 하드웨어가 동작(memory management unit)
- 최근에는 가상 메모리 관리 기능을 넘어서 가상 머신을 관리하는 기능까지 함(Virtualization)
- 페이징의 크기는 Fixed size 사용
- 기본적으로 우리는 4096의 CPU 크기를 사용함

### Page fault
- 필요할 때마다 잘라서 사용
- 잘라져 있는 것을 모아서 다시 자르기
- 메모리를 실제 사용할 때 까지 기다렸다가 실제 메모리를 사용할 때 작동
- 메모리의 위치가 RAM에만 있는지, HDD에도 있는지에 따라서 soft page fault와 hard page fault로 나눠짐
- page fault는 자연스러운 현상이지만 성능에 영향을 줌
- soft page fault(minor)
  - 디스크로부터 읽어지지 않는 경우(heap memory를 사용할 때)
- hard page fault(major)
  - 디스크로부터 읽어지는 경우(HDD를 사용할 때)
  
- memory locking 기법(POSIX.1b-1993)
  - paging을 막는 기법
  - swap을 결과적으로 막아줌
  - 중요한 정보들은 메모리에만 두고 디스크에 가지 못하게 하는 기능
  
### dirty
- modified page가 write-back 되기 전 상태(disk보다 memory가  더 최신 데이터인 경우(out of data 상태))
- CPU → cache → RAM → Disk 순으로 기록을 하는데
- dirty memory가 시스템에 있으면 어느정도 write에 유리
- Coherency: Cache-RAM-Disk는 항상 일관성이 유지되어야 함
- RAM-Disk의 inconsistency가 dirty page

### mmap
- 사용하는 메모리의 일정 부분과 디스크의 부분을 매핑해서 마치 메모리에 어떤 데이터를 넣게 되면 자동으로 디스크로 플러쉬
- File을 proces memory area에 mapping 시키는 법
- memory mapped I/O
- posix_madvise와 더불어 사용됨
- DB에서도 많이 사용

### in/out
- paging in: physical memory로 로딩(RAM에 들어온 것)
- paging out: physical memory에서 언로딩(RAM에서 빠진 것인데 swap out이 일어날 수도 있음)
- swap in: disk 공간에서 physical memory로 로딩
- swap out: physical memory에서 disk 공간으로 이동

### free memory
- free memory와 available memory는 다름
- free 명령어(구형 버전)
  - memory = Mem(physical memory) + Swap(disk)
  - total = used + free
  - free: 할당되지 않은 것이지 가용할 수 있는 것은 아님
  - 아래에 -/+ buffer/cache가 실제 사용할 수 있는 메모리
  - disk로부터 읽어온 데이터로부터 data는 cached / meta-data는 buffers에 저장
  - available memory = free + buffers + cached

- free 명령어(새로나온 버전)
  - Mem과 swap 두줄로 표현
  - 실제 사용가능한 메모리는 available 보기
  
- options
  - -k: 킬로바이트
  - -m: 메가바이트
  - -h: 사람이 읽기 편하게
  
- free 명령어에서 free 영역은 부팅 후 점점 줄어들음
- 그러다가 어느 수준에는 더이상 줄어들지 않음
- 파편화된 조각과 최대한 메모리를 캐시하기 때문

- 메모리의 실제 부족 여부를 알아보는 법
  - disk의 swap 영역에 사용량이 늘어나고 있을 때
  
- free vs meminfo
  - free 명령어는 개괄적인 데이터만 제공
  - memory 상태에 대해 자세한 정보를 알기 위해서는 /proc/meminfo를 분석할 수 있어야 함
  
- Available memory
  - User memory
    - Buffers + Cached + AnonPages*
    - Activ(file/anon) + Inactive(file/anon) + Unevictable
  
  - file, memory
    - https://sunyzero.tistory.com/260
  
  - free != available memory

- /proc/meminfo
  - cat /proc/meminfo
  - Active / Inactive
    - OS에서 최근에 사용한 것일수록 자주 사용됨(Locality)
    - 시간적으로 최근에 사용됬던 것이 자주 사용됨(Temporal)
    - 메모리마다 Age 부여
    - 나중에 메모리가 부족할 때 어떤 메모리를 빼앗을지 결정하기 위해서 사용
    - LRU 알고리즘에 의해서 최근에 사용된 페이지는 Active, Inactive
    - 메모리가 부족할 경우 Inactive로 분류된 메모리를 가져옴(release)
    - Inactive(anon)이라면 swap out, Inactive(file)이라면 disk에 original이 있으므로 즉시 전환
      
    - active memory(ananymous + file-backed): recently used
      - active(anon): 이름이 없음(외부 접근 불가능), 사본이 존재할 수 없음
      - activa(file): 사본 존재 가능
    
    - inactive memory(ananymous + file-backed): 나이든 메모리
      - inactive(anon): 이름이 없음(외부 접근 불가능), 사본이 존재할 수 없음
      - inactiva(file): 사본 존재 가능  

  - file-backed / anonymous memory
  
  - Dirty / Writeback
  
- /proc/meminfo: categories
  - Demanding memory 시 memory가 부족한다면 필요한만큼 메모리를 reclaim하는 과정이 진ㄴ행
  - 이 때 release cost가 가장 낮아서 release될 가능성이 높은 메모리는?
  - Inactive file-backed memory
  
# resource를 관리하는 법
### ulimit
- 사용자의 자원을 제한하는 것
- 멀티 유저 운영체제에는 자원을 나눠서 사용해야 함
- 즉 사용자의 자원을 제한하는 기능이 필요(ulimit)
- ulimit [-SHacdfilmnpqstuvx] [limit]

- option
  - -a: 현재 사용자 자원 제한 값을 모두 출력
  - -S<item>: item의 soft limit을 제한
  - -H<item>: item의 hard limit을 제한
  - -c #: core dump(단위: 블록)
  - -f #: 파일 1개의 크기(단위: 블록)
  - -l #: paging lock 할 수 있는 메모리의 크기(단위: KB)
  - -m #: RSS 메모리 제한 값(단위: KB)
  - -n #: 열 수 있는 file 개수(단위: 개수)
  - -t #: CPU time 사용 제한(단위: 초)
  - -u #: 프로세스 생성 개수 제한(단위: 개수)
  - -q #: POSIX MQ 최대 크기 총합(단위: B)
  - -p #: POSIX 1003.1b realtime signal 큐 길이
  
- soft vs hard limit
  - soft limit: 바꿀 수 있음
  - hard limit: 바꿀 수 없는 제한 값
  - hard limit의 크기 안에서 soft limit을 지정할 수 있음(soft limit <= hard limit)
  - -S나 -H의 soft/hard limit을 지정하는 옵션을 사용하지 않으면 소프트/하드 리밋을 동일하게 조정
  - 대체적으로는 hard limit은 잘 건들지 않음
  - core dump: 프로그램이 실행을 실패했을 때 남겨주는 dump 파일
  - coredumpctl: 코어 덤프 관리
  - cpu 시간 제한 예: 해당 프로그램이 무한 루프를 돌거나 CPU를 과도하게 사용할 때 사용
  
- 유용한 ulimit 사용
  - 무한 분기(fork)가 될 가능성이 있는 경우 테스트: ulimit -u 256
  - profile을 통한 자원 제한 가능(사용자가 profile을 편집할 수 있으므로 강제성은 없음)
  
- PAM을 통한 자원제한
  - login시에 제약 설정: /etc/pam.d/login
  - login시에 제약 설정: /etc/pam.d/system-auth
  - 설정 파일: /etc/security/limits.conf
    - domain: 대상이 되는 user
    - type: soft or hard
    - item: 자원 항목
    - value: 자원 값
  
  - 사용 가능한 item 목록들
    - core: core 파일 크기 제한
    - data: 데이터 영역의 메모리 크기 제한
    - fsize: 파일의 최대 크기를 제한
    - rss: RSS 메모리 영역의 크기 제한
    - stack: 스택 메모리 영역의 크기 제한
    - cpu: CPU 사용 시간을 제한
  
- limits.d
  - /etc/security/limits.conf를 직접 수정하지 않고 sources.list.d 디렉토리를 만들어서 한 것처럼 .d 디렉터리가 있다면 무조건 config를 넣는 곳으로 보면 됨
  
- Practice: PAM

# kernel param
### kernel
- kernel image, ramdisk
  - /boot 디렉토리 아래에 있음
  - linux는 windows와 다르게 version 별로 여러 개가 설치 되어 있음
  - 현재 커널이 부팅되면 시작되는 부분: /proc/sys/<class>  windows에서 registry
  
- classes
  - debug: debugging 설정
  - dev: device 설정
  - fs: 파일 시스템
  - kernel: 커널 기능 설정
  - net: 네트워크 설정
  - sunrpc: SUN RPC 기능(NFS 관련)
  - user: user namespace limits
  - vm: virtual memory
  
- Practice: /proc/sys/<class>
  - ls /proc/sys
  - ls /proc/sys/vm/
  - 파일은 cat으로 읽기(값을 변경할 때는 덮어쓰기)
  
- Document(공식문서)
  - Document(kernel)
    - https://www.kernel.org/doc/html/latest/
  
  - Document(sysctl)
    - https://www.kernel.org/doc/Documentation/sysctl/
    - https://sysctl-explorer.net/
  
- sysctl
  - 값을 읽어오는 기능: sysctl [options] [variable[=value]] [...]
  - 값을 setting 하는 기능
    - 하나씩 setting 하는 기능: sysctl -p [file pr regexp] [...]
    - 한번에 setting 하는 기능: sysctl -p --system [file pr regexp] [...]
  
- 부팅할 때 설정 잡아놓은 것을 자동으로 읽어오는 것
  - /etc/sysctl.conf(되도록 건드리지 않기)
  - /etc/sysctl.d/*.conf(사용자 추가 설정)
  
- sysctl kernel parameter 값 변경 예제
  
- net: tcp buffer, autotuning
  - net.ipv4.tcp_moderate_rcvbuf: TCP 소켓 버퍼에 TCP autotuning을 적용함(0=disable, 1=enable), 속도에 따라 자동으로 메모리 버퍼 크기 동적 조절
  - net.core.optmem_max: 소켓당 허용되는 보조 버퍼 크기(sendmsg의 cmsghdr 부가 정보)
  - net.core.rmem_default: 소켓의 기본 수신 버퍼 크기
  - net.core.wmem_default: 소켓의 기본 송신 버퍼 크기
  - net.core.rmem_max: syscall로 지정 가능한 TCP 소켓 수신 버퍼 제한 값(함부로 건들이면 안됨)
  - net.core.wmem_max: syscall로 지정 가능한 TCP 소켓 송신 버퍼 제한 값(함부로 건들이면 안됨)
    - 인터넷에서 무분별하게 이 설정의 max 부분을 16M으로 키우자는 문서가 많지만 사실이 아님
  - net.ipv4.tcp_rmem: TCP autotuning에서 사용(수신 버퍼의 최소, 기본, 최대 값을 지정)
  - net.ipv4.tcp_wmem: TCP autotuning에서 사용(송신 버퍼의 최소, 기본, 최대 값을 지정)
  
- TCP buffer: Rcv, Snd
  - TCP socket buffer size(window size): 무조건 크다고 좋은 것은 아님
  - 수신 측은 RTT에 따라 영향을 크게 받음(조금이라도 빨리 보내는 것이 나을 수 있음)
  - 송신 측은 혼잡에 따라 영향을 크게 받음
  
- RX window
  - recive window
    - 이미 보낸 데이터는 버퍼에서 제거
    - ack를 받지 못하면 buffer에 유지
    - sliding window: 구간을 결정하면서 이동하는 것
    - 송신측(peer)에게 알려주는 버퍼 수준은 sliding window
    
  - 수신측의 버퍼의 변화
    - 동적으로 변화시킬 수 있지만 자동으로 운영체제가 조절해주기 때문에 변화해줄 필요는 없음
    - max, limit가 크면 클수록 high RTT 상황에서 peer의 tx queue를 크게 상향시킬 수 있고, 그 결과 bufferbloat 효과를 발생시킬 수 있음
  
- Practice: low RTT
  - RTT가 짧은 시스템에서는 buffer를 크게 가져갈 필요가 없음
  - 수신을 잘 못하는 경우에 buffer에 계속 들어와서 buffer가 점점 늘어나는 경우가 발생함(수신큐에 데이터가 쌓임)
  
- RTT
  - 물리적으로 50~100km마다 1ms 정도 늘어난다고 생각(항상 그렇지는 않음)
  - 국내는 5ms 내외(제주도 10ms 내외), 수도권 1~2ms의 작은 값
  
- timestamps
  - net.ipv4.tcp_timestamps
    - 1: on(default)
    - 0: off
  
  - 이 기능은 timer을 사용하기 때문에 server에서 끄면 위험
  
- time-wait
  - npt.ipv4.tcp_tw_resue
    - 1: on
    - 0: off(default)
   
  - TIME_WAIT는 보통 ACTIVE CLOSE 측에서 생기는데 Server가 먼저 끊는 경우가 있음
  - 서버를 재활용하지 않으면 소켓이 부족해질 수 있기 때문에 on인 경우 TCP 소켓을 재활용할 수 있도록 함
  
- listen backlog
  - net.core.somaxconn
    - listen을 받아주는 queue의 길이
    - 2의 승수로 지정
  
  - net.ipv4.tcp_abort_on_overflow
    - 더 많은 접속이 들어올 때 abort 기능이 있음
    - overflow가 된 순간 상대편에게 abort
    - default: off(0)
  
- TFO
  - Tcp Fast Open
  - Google에서 3 hand shaking 할 때, 데이터를 실어서 보내자는 제안
  - net.ipv4.tcp_fastopen
    - 0: off
    - 1: client only
    - 2: server only
    - 3: client + server
  
- dirty memory
  - dirty memory가 있으면 기록이 빨라짐
  - /proc/sys/vm
    - **dirty_ratio**
      - 전체 메모리 중에서 dirty page가 차지하는 mex ratio(default: 30~40%, min: 5%)
      - dirty page가 이 값에 도달하면 sync mode로 디스크에 기록하게 됨
      - 낮은 dirty ration는 시스템의 캐시 효과를 무력화 시킬 수 있음
      
    - **dirty_backgroud_ratio**
      - pdflush가 dirty page을 기록 시작할 tot, avail, mem의 비율(default 10%)이 값이 커질 수록 덜 자주 기록하게 되어 latency가 높아질 수 있음
    - dirty_bytes: dirty page의 max byte 크기, 값이 설정되면 ratio 보다 먼저 우선순위를 가짐
    - dirty_background_bytes: dirty_background_ratio와 같은 기능이지만 byte로 지정
    - dirty_writeback_centisecs: dirty page 체크 시간 인터벌(default 500=5sec)
    - dirty_expire_centisecs: dirty expire될 때까지의 grace period(default 3000=30sec)
  
- kernel parameter를 변경하는 방법
  - sysctl로 변경하는 방법
  - /proc/sys 밑에 있는 맵 파일을 직접 변경하는 방법
  - sysctl로 직접 변경하는 것은 좋은 방법이 아니고 tuned의 profile 조정이 더 나음
  
- swap
  - vm.swappiness
  - default: 30
  - 작으면 작을수록 swap을 피하고 page cache 쪽에서 가져오도록 함
  - 만일 0이되면 page cache를 빼앗는 작업을 자주하여 오버헤드가 발생할 수 있음
  - 특별한 경우가 아니라면 기본값을 바꾸지 않는 것이 좋음
  - 일반적으로 빠른 network 통신용 시스템에서는 10을 사용함
  
# Kernel parameter: Tuned
- tuned
  - dynamic adaptive system tuning daemon
  - 따로 설치해줘야 함
  - tuned-adm: param 값을 설정해줌
  
- tuned installation
  - RHEL 계열: yum -y install tuned
  - Debain 계열: apt -y install tuned
  
- endable & start
  - systemctl enable --now tuned(--now: start)
  - systemctl status tuned
  
- tuned-adm
  - tuned-adm list: Available profiles 나옴
  - tuned-adm recommend: 시스템에서 필요한 것을 추천 받음
  - tuned predefined profile
    - balenced: 균형 모드(적당한 파워, 성능, 전원)
    - **desktop**: 일반적으로 power saving을 해줌
    - laptop-ac-powersave
    - laptop-battery-powersave
    - powersave: powersave mode로 하면 ethernet 속도가 100M로 떨어짐
      - 고성능 네트워크를 사용하는 것은 그만큼 전력을 많이 사용하기 때문에 네트워크 전력을 많이 먹음
      - IoT나 관련 장치에서 주로 사용하며 일반적인 서버의 경우에느 추천하지 않음
      
    - virtual-guest
    - virtual-host
    
  - tuned predefined profile(서버나 고성능)
    - **latency-performance**: 응답 속도가 빨라짐(파워 소모량이 증가할 수 있음)
    - network-latency: 빠른 네트워크 응답이 필요할 때 사용(server 용)
    - **network-throughput**: 대용량 데이터를 주고받을 때 유리
    - **throughput-performance**: (default)처리 용량을 빠르게 하는데 중점
    - enterprise-storage: 디스크 처리를 더 빠르게(I/O)
  
- profile
  - 현재 active된 profile 보기: tuned-adm active
  - profile 변경: tuned-adm profile throughput-performance(tab 지원)
  - sysctl -a > tp_profile.txt
  - sysctl은 자주 사용하지 않고, tuned에서 여러가지 profile을 작성해서 탄력적으로 사용하는 것이 유용
  
- profile dir
  - /usr/lib/tuned
  
- custom tuned.conf
  - [cpu] governor
    - CPU model에 따라 지원되는 governor list가 다름
    - performance: 최고의 성능을 위해 clock을 정적으로 사용(최대 클럭으로 작동)(x86)
    - powersave: 저전력을 위해 동적 clock 사용, 최고 성능 클럭으로 도달하는데 느림(x86)
    - ondemend: 동적 클럭인데 위로 올라갈 수록 빠름(Arm)
    - conservative:  동적 클럭인데 천천히 올라감, 전력을 아끼면서 작동(Arm)
  
  - cpupower 툴로 frequency, governor를 확인할 수 있음
    - cpupower는 실제 CPU를 조회하므로 가상 머신에서 동작하지 않음
    - 우분투 generic 커널 사용시 linux-tools-generic 설치
  
  - [cpu] min_perf_pct, max_perf_pct
    - CPU의 hardware limit 성능을 percentage로 제한
    - exmaple
  
  - DB(heavy FS/NET)
    - /usr/lib/tuned/mydb/tuned.conf
  
# Device:Processors(CPU, GPU)
### CPU
- command
  - lscpu: cpu의 정보 보기
    - -a: all
    - -b: online
    - -c: offline
    - -e [list]: extend, list에 나온 것들만 출력
      - list: CPU, CORE, SOCKET, BOOK, DRAWER, NODE, CACHE, ADDRESS, ONLINE, CONFIGURED, POLARIZATION, MAXMHZ, MINMHZ
      
    - -p [list]: parse
    - practice(arm)
    - practice(x86:intel)
      - Thread를 두배속 가져가는 기술: HyperThreading, SMT
  
    - practice(lscpu -p)
      - 프로그램 처리가 용이한 CSV 포맷
      - csv data foramt
      - 순서: CPU, Core, Socekt, Node, L1d. L1i, L2, L3  
  
    - practice(lscpu -e)
      - 사람이 읽기 편한 방식으로 나옴
      - 원하는 정보만 뽑아낼 수 있음
    
  - chcpu: cpu를 on/off 하는 기능
    - 특정 core를 활성화/비활성화 시키는 기능
    - -e: --enable cpu-list
    - -d: --disable cpu-list
    - comma로 구분되거나 - 사용 가능
    
    - practice: online/offlinie
      - 보통 0번 CPU는 끌 수 없음
      - cat /sys/devices/system/cpu/cpu*/online으로 접근할 수 있지만 좋은 방법은 아님
    
  - cpupower: cpu의 power 관련 정책 결정
    - cpu의 전력 및 클락, 작동 정책 등을 관리
    - 커널 관련 툴 패키지에서 제공
      - RHEL 계열: kernel-tools
      - Debain 계열: linux-tools-*
    
    - 가상 머신에서 작동하지 않음
    - cpupower [options] <commnad> [ARGS]
      - c <cpu list>
      - command
        - frequency-info, frequency-set
          - ARM 계열은 socket이 나눠져 있을 수 있음
          
        - idle-info, idle-set
        - set
        - info
        - monitor
          - state가 어디서 머무르고 있는지 확인 가능

### GPU
- nvidia
  - nvidia GPU: stats 확인하기
    - nvidia-smi
    - pcp-pmda-nvidia-gpu(제일 복잡하면서 제일 고급 레벨에서 사용)
      - pcp, pcp-manager가 설치되어야 함
      - nvidia: /var/lib/pcp/pmdas/nvidia 디렉토리
      - NVML 라이브러리가 제대로 인식되지 않는 경우에는 직접 NVML 심볼릭 링크를 생성해야 함
      
    - python: gpustat(python에서 gpustat 설치해서 사용)
  
- sensors
  - 시스템에서 제공하는 센서: 온도, 전압, RPM 등
  - 패키지 설치해줘야 함
    - RHEL: lm_sensors
    - Debian: lm-sensors
  
  - 설치 후 sensor를 detect하는 과정이 필요
    - 하드웨어가 변경되면 다시 detect
    - sensors-detect
    - detect 된 이후에 실행하면 센서 정보를 출력