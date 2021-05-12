---
title: "리눅스 기초"
excerpt: ""

categories:
  - UNIX
tags:
  - python
  - 자료구조
  - 알고리즘
  - Data Structure
  - Algorithm
---
# Package Manager
- Redhat Package System
  - **rpm** database
  - **yum**
  - dnf
- Debain Package System
  - **dpkg**, dselect
  - apt-get, apt-cache
  - aptitude
  - **apt**
  
- Package
  - 시스템을 구성하는 파일의 묶음
  - 패키지 관리자(or 설치 프로그램)에 의해서 읽혀지는 pre-built 파일들
  - 관리(설치, 삭제, 업그레이드, 질의)의 편리함
  
- 리눅스 패키지 방식
  - RPM: 레드햇 계열(레드햇, 페도라, 센토스...)
    - rpm(Red Hat Package Manager): 기본 명령
    - yum(yellow-dog updater modifier): 네트워크, 의존성 설치 지원툴
  - DEB: 데비안 계열(데비안, 우분투, 민트...)
    - dpkg(debian package manager): 기본 명령
    - apt(advanced package tools): 네트워크, 의존성 설치 지원툴
  - Debian 계열은 apt를 쓰고, RH 계열은 yum을 사용하게 됨
  
- Debian Package manager의 구조: UNIX의 pkg에서 유래
  - package name
  - version & release
  - arch(아키텍처)
  
- dpkg
  - 초기 debian에서부터 유래된 가장 기본적인 패키지 관리자
  - dependency를 해결하지 못하고 검색도 어려우며 네트워크 설치를 제대로 지원하지 않음
  - 이 후, 상기 문제점들은 APT로 해결
  - APT 중점으로 다루고 APT에서 모자란 기능만 dpkg로 배우기
  
- 패키지 리스트 확인
  > dpkg -l strace
  > dpkg -l gcc
  
- query
  > dpkg -s <pacage_pattern ...>
  - s: 패키지 상태 확인
  > dpkg -S <pattern ...>
  - S: (대문자) 패키지 검색: 파일명
  
- audit: dpkg 에러 해결 명령

# APT
- apt(advance package tool)
  - debian의 dpkg를 랩핑한 front-end tool(네트워크 설치 지원, dependency 탐색 및 설치 가능)
- binary
  - apt-get: install / remove / update에 사용
  - apt-cache: query에 사용
- binary-extensions
  - apt-file
- new binary
  - **apt**(위에 것들은 통일성이 부족하기 때문에 요즘에 사용하는 tool)
  
- apt: source list
  - source list: apt가 package를 가져오는 것(apt가 package file을 다운 받는 곳의 정보)
  - /etc/apt/source.list
  - 추가할 경우 /etc/apt/source.list.d/ 에 *.list 파일명으로 추가하는 편
  - 편집 시 apt edit-sources 명령어로 가능(vim을 열어서 직접 편집할 수 있지만 되도록 apt edit-sources 명령 사용)
  
- source.list format
  - deb[ option1=value1 option2=value2 ] uri suite [component1] [component2] [...]: debian 패키지를 받아오는 주소
  - deb-src[ option1=value1 option2=value2 ] uri suite [component1] [component2] [...]: 소스 코드를 받아오는 주소
  - uri: DEB 패키지를 제공하는 사이트의 URI
  - suite: distribution codename 디렉터리 이름
    - 16.04 = xenial, 18.04 = bionic
    - https://wiki.ubuntu.com/Releases
  - component: suite의 구성 요소 및 라이선스 종류별 분류
    - **main**: 우분투에서 직접 패키징을 하는 main 패키지들
    - **restricted**: 완벽한 free 라이선스가 아닌 것들
    - **universe**: 완전하게 지원하지는 않지만 절반정도 officially 지원하는 것
    - multiverse: non-free
    - security: Important security updates
    - updates: recommended updates
    - proposed:proposed updates
    - backports: unsupported
  
- /etc/apt/sources.list.d/kr.list 처럼 sources.list.d에 파일을 만들어서 작업

- apt list [options] [package pattern]: 패키지 목록 출력
  - 옵션이 지정되지 않으면 해당 버전에서 제공되는 패키지의 최신 목록 출력
  - --installed: 설치된 패키지 리스트
  - --upgradalbe: 업그레이드가 가능한 패키지 리스트
  - --all-versions: 모든 버전의 패키지 리스트
  - bash*: bash라는 단어로 시작하는 패키지 리스트
  
- apt search [-n] <regex>: 패키지를 키워드로 검색, 키워드는 REGEX로 입력
  - n: 검색 대상을 name 필드로 한정
  - apt search bash: name이 아닌 description에 bash가 들어간 경우까지 검색함
  - apt search -n bash: name에 중간에 bash가 있어도 검색
  - apt serch -n '^bash': name의 시작 부분에 bash가 있는 경우만 검색
  
- apt show <package name>[=version]: 패키지 정보 출력
  - apt show bash: bash의 정보가 나옴
  - apt list --all-versions bash: bash의 모든 버전의 정보
  - apt show bash=4.4.18-2ubuntul: 특정 버전의 정보를 볼 수 있음
  
- apt <remove|purge|autoremove> <package>[=version]
  - remove: 패키지만 삭제(config 파일은 남겨둠)
  - purge: 패키지 삭제(config도 삭제, 설정이 꼬일 때)
  - autoremove: 의존성이 깨지거나 버전 관리로 인해 쓰이지 않는 패키지를 자동 제거
  
- pcp 패키지를 검색 및 설치
  > # apt search -n '^pcp*'
  > # apt-cache pkgnames pcp
  > # apt install pcp -y
  
- apt dependency error
  - 일반적으로 apt의 sources.list 설정에서 components에 universe를 빼먹은 경우 생김
  - universe를 추가하는 방법도 있지만 downgrade 하는 방법도 있음
  > # apt list --all-versions libcurl3-gnutls
  모든 버전 확인 후, 하나 낮은 버전 설치
  > # apt install curl libcurl3-gnutls=7.47.0-lubuntu2
  
- aptitude(TUI 방식의 apt)
  - ncurses based tool
  - apt-cache, apt-get 명령 포함
  - C-T: Ctrl-T: menu on/off
  - 사실상 그다지 많이 사용되지 않음
  
# Linux network system
- 네트워크 용어 정리
  - hostname: primary hostname, FQDN
    - 사람의 이름과 컴퓨터 이름 비교
      - 사람의 이름: Richard Stevens
      - 컴퓨터의 이름: access.redhat.com
    - 호스트 이름(hostname)
      - 사람의 이름(given name + family name)에 해당하느 ㄴ것
    - 도메인 주소(domain address)
      - 사람의 성(family name)에 해당하는 것
      - ex) Stevens = redhat.com
    - Hostname에는 중의적 의미가 있음
      - domain을 제외한 호스트 이름
      - domain을 포함한 이름(FQDN)
    - FQDN(Fully Qualifed Domain Name) → DNS 서버에서 설정
      - 도메인 내에서 유일하게 구별 가능한 이름(도메인 이름은 중복 불가)
      - 도메인은 체계적인 구조를 가짐
        - kr(한국의 주소), or(단체), fclinux(단체 이름), devel(단체 내에서 유일한 컴퓨터 이름)
    - special hostname
      - localhost
        - 항상 자기 자신을 의미하는 주소와 맵핑
        - IPv4: 127.0.0.1 / IPv6: ::1
    
  - TCP/IP: IP address(IPv4, IPv6), subnet mask, gateway
    - IPv4
      - version 4
      - 32bit 주소 체계: 8bit씩 끊어서 xxx.ooo.xxx.ooo 식으로 읽음
      - IPv4의 클래스: A, B, C, D, E(지금은 사라짐)
    - IPv6
      - version 6
      - 128bit 주소 체계
      - IPv4의 주소가 부족한 현상으로 인해서 사용
      - 국제적으로는 IPv6를 많이 사용하도록 규제하는 방향
      - IPv4-mapped IPv6(::ffff:IPv4_addr)
    - dual stack
      - IPv4와 IPv6 모두 사용하기 위해서
      - http://gsyc.escet.urjc.es/~eva/IPv6-web/ipv6.html
      - 대부분 Modern OS는 dual stack을 지원함(Windows XP 제외)
      - IPv4 전용 프로그램이 혼용이 되면서 여러 가지 문제가 있음
    - CIDR(Classless Inter-Domain Routing)
      - IP 클래스와 상관없이 서브넷을 지정하여 자르는 것을 의미
      - xxx.ooo.xxx.ooo/##으로 표기(##에는 서브넷 마스크의 on 비트의 개수를 표기)
      - 192.168.100.0/24 → 192.168.100.0/255.255.255.0
      - 192.168.100.0/25 → 192.168.100.0/255.255.255.128
    - IP 주소
      - public IP: 공인 주소(인터넷에서 유일한 주소)
      - private IP: 사설 주소(인터넷에 직접 연결되지 않은 유일하지 않은 주소)
  - NIC: Network Interface Card(= 랜카드)
  - Wired Network(Wired connection): 유선 네트워크(유선 연결)
  - Wireliss Network(Wireless connection): 무선 네트워크(무선 연결)
  - LAN: Local Area Network
  - WAN: Wid Area Network
  
  - SELinux와 네트워크 서비스
    - Securiy Enhanced Linux: 커널 레벨에서의 중앙 집중식 보안 기능
      - 대부분의 리눅스는 default 설치, Ubuntu는 설치되지 않음
    - SELinux의 보안레벨
      - enfocing(강제): SELinux를 이용, 보안 설정에 걸리면 강제로 막음
      - permissive(허가): SELinux를 이용, 보안 설정에 걸리면 허용하되 로그 출력
      - disabled(비활성): SELinux를 사용하지 않음
    - 서버 구성시에는 SELinux에 대한 이해가 필요하기 때문에 실습에서는 permissive 레벨로 내리는 것이 좋음
  
# 네트워크 실제 설정Network configuration(Debian)
- Legacy 방식(요즘은 사용하지 않음)
  - Debian 계열 legacy config: /etc/network/interfaces
  - RH 계열 legacy config: /etc/sysconfig/network-scirpts/*
  - Legacy 방식을 배우는 이유
    - legacy 설정을 사용하는 커스텀 리눅스를 사용하는 경우
    - legacy를 사용하면 안되는데, 잘못된 인터넷 검색으로 각종 오류의 원인이 될 수 있기 때문
  > auto lo
  > iface lo inet loopback
  > auto eth0
  > iface eth0 inet dhcp
  > /etc/init.d/networking
  > /etc/init.d/networking restart
  > ifdown -a && ifup -a
  > ifconfig
  
# **Network Manager**
- NetworkManager의 장점
  - daemon으로 작동하여 network configruation을 수행하고 자동으로 connection을 관리(연결 감지, 해제, 재시도)를 수행
  - Dbus 기반으로 동적 상태를 감지할 수 있기 때문에 다른 애플리케이션이나 daemon들에게 네트워크 정보를 제공하거나, 관리를 위한 권한을 줄 수 있음
  - systemd 기반의 다른 Linux distribution들에도 동일한 경험을 제공할 수 있음
  - Ethernet, WiFi, Mobile broadband 등 다양한 기능들에게 플랫폼을 제공하므로 네트워크 연결 관리가 좀 더 쉬워짐
  
- 과거에 사용되던 명령어
  - UNIX standard command(POSIX)
    - ifconfig
    - rout
  - Non-standard command(Linux specific)
    - ip
    - nmcli
    - ethtool, pifconfig & pethtool(python-ethtool)
  
- nmcli: Network Manager CLI tool
  - 네트워크에 관련된 대부분의 기능을 가지고 있음
  - 조회 및 설정 가능
  
- nmcli g[eneral]
  - STATE: connected / asleep
  - CONNECTIVITY: full / none
  
- nmcli n[etworking]: 네트워크 상태 조회
- nmcli n on: 네트워크 키기
- nmcli n off: 네트워크 끄기

- device 확인
  - nmcli d[ev]
  - DEVICE, TYPE, STATE, CONNECTION이 나옴
  - device name인 ens33
  - device naming
    - eth#[:n]
      - 과거 시스템: eth0, eth1, eth0:0, eth0:1
    - 서버는 이더넷 장치가 복수로 있는 경우가 많은데 어느 것이 0인지 1인지 알기 어려웠음
    - Consistent Network Device Naming(new naming)
      - prefix
        - en: ethernet 
        - wl: wireless lan
        - ww: wireless wan
      - following device name
        - o<index>: on-board device index number
        - s<slot>[f<function>][d<dev_id>]: hotplug slot index number
        - x<Mac>: MAC address
        - p<bus>s<slot>[f<function>][d<dev_id>]: PCI geographical location
        - p<bus>s<slot>[f<function>]u<port>[..][c<config>][i<interface>]: USB port number
        - ex) enp5s0: ethernet, PCI bus 5, 0번 slot
  
- nmcli <g | n | r | c | d> []
  - general, networking, radio, connetion, device
  
- nmcli c[onnection] s[how]: connection을 자세히 보기
- nmcli c[onnection] s[how] <connection name>: 특정 connection name에 설정된 속성 자세히 보기
  - ipv[46].*: 설정된 값(소문자 → offline일 때도 보임)
  - IP[46].*: 할당된 값(대문자 → online일 때만 보임)
  - 주요 속성
    - ipv4.method: ip를 할당받을때 어떤 주소를 쓰는지
      - auto | manual
      - auto = dhcp
      - manual = static ip
    - ipv4.addr
      - IPv4 address CIDR 표기법(= 192.168.110.50/24)
    - ipv4.getway
      - Getway IP
    - ipv4.dns 
      - DNS server IP
  - optional prefix of property
    - +: 기존값 유지하고 추가적으로 사용
    - -: 기존값 제거
    - none: 현재 값 교체 
> # nmcli con modify "Wired connection 1" connection.id ens33
> # nmcli c mod ens33 ipv4.method manual ipv4.address 192.168.176.129/24 \
> # ipv4.gateway 192.168.17.2 +ipv4.dns 8.8.8.8
> # nmcli c s ens33
> # nmcli c down ens33 && nmcli c up ens33

virtual IP 추가
> # nmcli c mode ens33 +ipv4.addresses 192.168.110.181/24
> # nmcli c up ems33

IP 삭제
> # nmcli c mode ens33 -ipv4.addresses 192.168.110.181/24
> # nmcli c up ems33

기존 설정 삭제
> # nmcli c del ens33
> # nmcli c add con-name ens33 ifname ens33 type ethernet \
> # ip4 192.168.110.161/24
> # nmcli c s

DNS 추가
> # nmcli c mode ens33 +ipv4.dns 8.8.8.8
> # nmcli c s ens33 | egrep '(ipv4.addr|ipv4.gateway|ipv4.dns)'

- device 자체가 올라오지 않는 경우
  - nmcli dev s 했을 때 disconnected 되어 있을 때: nmcli dev connect<device name>
  - nmcli c 화면에서 DEVICE란이 --로 비어있을 때: nmcli networking on 네트워크를 다시 켜주기
  - 이래도 안되는 경우라면 lshw, lspci, lsusb, dmesg 등으로 하드웨어가 인식된 상태인지 검사
  
#Network: Socket Status
- net tools: netstat(구식 명령)
- 성능이나 여러 가지 문제점이 발견됐기 때문에 사용하지 않음(netstat의 모든 기능은 ss가 흡수)
- network status: 네트워크의 상태를 확인하는 유틸리티
- tcp connections: netstat -nt
- tcp listen port: netstat -ntl

- socket statistics
  - ss [options] [FILTER]
    - n: numberic
    - a: all
    - l: listening
    - e: extended
    - o: options
    - m: memory
    - p: process
    - i: info
    - s: summary
    - 4: ipv4
    - 6: ipv6
    - t: tcp 
    - u: udp
    - x: unix
    - -f FAMILY, --family=FAMILY
  - FILTER에는 특정 state, address를 지정할 수 있음
    - FILTER := [state TCP-STATE][EXPRESSION]
    - TCP-STATE
      - established
      - fin-wait-2
      - close-wait

- TCP State Transition Diagram
  - 사진 나옴
  - 정말 중요한 내용
  - TCP/IP Illustrated Vol.1 R.Stevens 매우 중요한 책
    - 실선: client의 흐름 / 점선: server의 흐름
    - 이것은 초보자가 이해하기 어려움
  - 강사님이 다시 만드심
  
- TCP State transition
  - Three-way handshaking
    - TCP 접속을 만드는 과정
    - 3번 왔다갔다 하기 때문에 이렇게 불리움 
    - 운영체제가 보통 처리하기 때문에 문제가 생기는 일이 거의 없음
  - Four-way handshaking
    - TCP 접속을 해제하는 과정
    - 4번 왔다갔다 하기 때문에 이렇게 불리지만, 아주 드물기 동시에 접속이 해제되는 경우는 3번 만에 끝남
    - 문제가 종종 생김(특히, Passive close 하는 측에서 문제가 생김(CLOSE_WAIT 상태에 빠짐))
  
- ss -nt(numberic, tcp): tcp connection을 보는 기능
- State: ESTAB(Established)
- IPv6에서 ::ffff: prefix가 있으면 v4처럼 생겼지만 v6이라는 것 의미

- ss -ntm: 메모리를 보는 기능
  - r: 읽기 메모리
  - w: 쓰기 메모리
  - f: 할당된 메모리
  
- ss state <tcp state>: <tcp state>의 경우의 상태만 보여주기

- close-wait와 fin-wait-2가 발생하면 심각한 문제(한 쪽이 생기면 반대쪽이 생김)
- 소켓을 닫지 않았기 때문에 소켓에 할당된 리소스가 그대로 누수된 상황으로 굉장히 위험
- close-wait: 내가 잘못한 경우, fin-wait-2: 상대방이 잘못한 경우
- ss -nt state close-wait state fin-wait-2
- watch -d -n 1 ss -nt state close-wait state fin-wait-2(1초마다 자동으로 띄우기)
terminam에 이렇게 띄워놓고, firefox 띄워보기
  
- address filter
  - IP address, CIDR, Port number에 따른 address 범위, 값 지정 가능
  - symbolic, literal 방식 지원
  
  |symblic|literal|descrpition|
  |:---|:---||
  |>|gt|greater than|
  |<|lt|less than|
  |>=|ge|grater than or equal|
  |<=|le|less than or equal|
  |==|eq|equal|
  |!=|ne|not equal|
  ||and|and operator|
  ||or|or operator|
  
- ss -n dport = :22 (상대편 포트가 22인것을 찾아내라)

- ss -s: 통계 정보(statistics)

- ss -p: 해당 프로세서의 정보(Show process using socket), 일반 유저보다 root 유저일때 더 자세한 정보가 나옴

- addr/port filter
  - ss -n 'src 192.168.110.134': 192.168.110.134 주소에서 접속하는 경우만 살펴보기
  - ss -n 'src 192.168.110.134 and 192.168.110.167': 여러 개의 주소를 and로 결합
  - ss -n '192.168.110.0/24': bit masking으로 24비트까지만 봐서 같은 연산자 찾기(여기서는 110까지만 보겠다는 얘기)
  - ss -n 'dport = :ssh or sport =: ssh': 내 port가 ssh이거나 상대편 port가 ssh
  - ss -n '( dport = :ssh or sport =: ssh ) and src 192.168.110.134'
  
- greater / less
  - ss -n 'dport > :8000'
  - ss -n 'dprot > :8000 and dport < :20000'
  - ss -nlt 'sport le 1023'
  - netstat에서 출력된 결과를 특정 조건에 맞도록 sed, grep, awk로 후처리 하는 방식은 상당히 성능이 떨어짐
  
- -i: internal TCP info
- ss -nt -i
  
- lsof, fuser(list open file, 특정 user가 열고 있는 파일 검색)
  - 열린 파일을 검색하거나 액션을 행할 수 있는 기능을 가짐
  - 특정 소켓 주소를 점유하고 있는 프로세스를 찾아내거나 할 때 사용(나중에 많이 사용함)
  
# querying
- ping: 상대 호스트의 응답확인(호스트의 존재 여부 확인)
  - ping [-c count] [-i interval] [-s size] [-t ttl] target
  - 현대적인 시스템에서 방화벽이나 보안이 높은 곳은 ping을 모두 막아서 요즘 자주 사용하지 않음
  - ctrl-c로 종료하면 마지막에 통계 정보를 알려줌 min/avg/max/mdev
  - 0.2 sec 간격으로 2000번 테스트
  > $ ping -c 2000 -i 0.2 -s 1000 192.168.1.1
  - 표준편차가 너무 크면 널뛰기를 하는 경우이기 때문에 신경써야 함

- traceroute: 패킷의 도달 경로를 확인
  
- arp: arp 테이블 보기
  - Address Resolution Protocal: IP 주소와 MAC 주소(Hardware address)의 매칭 테이블
  - 우리가 사용하는 IP 주소는 L3, 이더넷은 L2
  - arp -s <MAC address> <IP address>: 특정 IP 주소에 특정 MAC 주소 매핑, static arp address를 사용하는 경우
  
- NIC(네트워크 카드) 교체 후 통신이 실패하는 경우
  - 고정 IP 주소를 사용하는 기관이나 회사의 경우에, 보안이나 IP 주소 관리를 위해서 고정 ARP 테이블을 사용하는 경우가 있음
  - APR 테이블(arp로 확인)을 확인하여 IP와 MAC 주소(ifconfig/nmcli로 확인)가 제대로 매칭되는지 확인할 것
  - 네트워크 관리자에게 교체한 NIC의 MAC 주소와 IP를 새로 알려주어야 함
  
- resolver: name service: IP address 혹은 hostname(FQDN, alias)를 해석
  - nameserver를 의미
  - /etc/resolv.conf에 지정
  - nslookup(요즘 사용 x)
  > $ nslookup www.naver.com
  - dig
  - dig [@server] <target>
  - <target>을 지정하지 않으면 /etc/resolv.conf에 질의
  > $ dig www.google.com
  > $ dig @8.8.8.8 www.nate.com
  
  - 유명한 nameserver
    - 1.1.1.1: Cloudflare DNS
    - 208.67.222.222: CISCO openDNS
    - 8.8.8.8: google DNS
  
- ethtool: 네트워크 카드에 대해서 질의하거나 설정을 잡는 툴
  - ethool [장치명]: 장치명에 관한 설정을 알려줌
  - 간혹 시스템이 느려지는 경우에는 duplex 문제일 가능성이 있음
    - speed가 떨어지고 Duplex가 Half로 잡혀있으면 설정을 다 사용하지 못하는 것
    - 외부에 프로그램이 강제로 바꾸는 경우(powersave 모드 작동 시 적응할 수 있음)
    - ethtool -s enp0s31f6 speed 1000 duplex full 수동 명령으로 바꿔줌
  - ethernet auto-negociation: 자동으로 link 속도를 협상하는 기능
    - a: --show-pause
    - A: --pause
    - ethtool -s enp0s31f6 speed 1000 duplex full autoneg off
  - ethtool -A|--pause devename [autoneg on|off] [rx on|off] [tx on|off]
  - statistics: 통계 정보
  - ethtool -s [장치명]
  
- offloading: 성능에 관련된 부분(cpu가 계산해야 하는 부분을 ram이 대신 해줌)
  - k <dev>: show offloading
  - K <dev> <param> <on|off>
  
# ssh server
- ssh(secure shell): 통신 구간의 암호화
  - 암호화 되지 않은 통신을 사용하던 telnet 서비스 대체
  - telnet이 하던 프로토콜 테스트 기능은 nc, curl 대체
  - 기본적으로 리눅스 서버들은 ssh 서비스가 탑재되어 있지만 없는 경우 설치해주기
  - Linux의 ssh는 openssh 사용
  
- sshd
  - ssh daemon, 즉 ssh server
  
- ssh
  - ssh client이며, ssh 명령어가 바로 ssh client CLI 유틸리티
  - Linux나 UNIX 계열에서는 ssh client GUI가 따로 제공되는 것이 아니라 ssh CLI 명령어로 실행
  - 하지만 MS 윈도우 계열에서 접속하려면 putty, mobaXterm 같은 툴 사용
  
- sshd 서버의 준비 작업
  1. sshd 서버의 설치 여부 확인
    - debian 계열: # apt list openssh*
    - RH 계열: # rpm -qa openssh-server
  2. sshd 서비스가 실행 중인지 확인
    - Listen 상태 확인: ss -nlt or ss -ntlp
    - systemd 기반이면 systemctl status sshd
    - ini 기반(구형 리눅스)이면 service sshd status
    - active (running)이면 잘 실행 중
    - sshd 서비스가 정지된 상태인 경우
      - systemd 기반: systemctl start sshd
      - init 기반: service sshd start
    - 부팅될 때 sshd 서비스가 실행되고 싶으면: systemctl enable sshd
    - 부팅될 때 sshd 서비스가 실행되면서 start까지 동시에 하고 싶으면 systemctl enable sshd --now 
    - ssh port 22번의 주소가 2개인 이유: ipv4(*: 0.0.0.0), ipv6(:::)
  3. ssh port(22/tcp)가 방화벽에 허용되어 있는지 확인
    - iptables(혹은 firewall-cmd, ufw) 이용
    - iptables -nL
  - 우분투에서 방화벽을 끄는 방법
    - ufw -h
    - ufw status
    - ufw enable
    - ufw disable
    - ufw status verbose
  - 방화벽을 켜주고 싶을 때
    - allow <port number|port symbol>[/protocol]
    - ufw allow 22/tcp
    - ufw allow http/tcp
  
- ssh client
  - Linux 혹은 UNIX like 시스템은 ssh 사용
  - ssh [-p port] [username@]<host address>
  - port 번호를 사용하지 않으면 정규 포트 22번
  - id 이름을 사용하지 않았을 때는 로그인한 user 명으로
  - ssh 192.168.52.110
  - ssh linuxer@192.168.52.110
  - ssh -p 20022 192.168.52.110
  
- putty, mobaXterm

- -N: 키 기반 통신
  - 개인용에서는 password를 쓰지 않지만, 기업에서는 꼭 ""대신에 pw 사용
  - key 위치: ~/.ssh
  - ssh-keygen -N ""
  - ssh-copy-id muk1975@192.168.52.110 ← 접속할 상대
  - ssh -t muk1975@192.168.52.110 w: muk1975@192.168.52.110에 w 명령을 내리고 나한테 결과 보여주기
  - p: 암호 변경
  - R <host>: known_host에서 remote host의 키 제거
  
- HTTP/others utils
  - curl
    - URL 기반으로 통신하는 기능 제공
    - HTTP, HTTPS 등
    - libcurl 라이브러리 제공: 다양한 프로그래밍 언어에서 프로그래밍 가능
    - curl [options] <URL>
    - curl -O <URL/파일명>: 파일명 이름으로 저장
    - curl -O 파일명 <URL>: 파일명 이름으로 저장
    - curl -C - -O <URL>: 중간에 끊겨도 -로 남은 부분 계산해서 다운로드 continue
    - curl <API URL>: API 받아오기
  
  - wget <URL>
    - curl과 대부분 기능이 비슷하나, curl이 더 많은 기능을 가짐
    - 파일 다운로드에 특화된 기능 존재
    - mirroring 기능 (web site 복제)
  
  - nc(netcat)
    - network 기능이 가능한 cat
    - server, client의 양쪽 기능이 가능
    - 간이 서버, 클라이언트로 사용 가능
    - nc -k -l 5000   TCP 5000번
    - echo Hello | nc 127.0.0.1 5000
    - nc 127.0.9.1. 5000
  
# 무선 네트워크 설정
- WiFi
  - NetworkManager(NM) 도입 이후 통합
  - wicd는 deprecated
  
- nmcli radio: 무선 네트워크 기능 활성화 여부 확인
  - nmcli r wifi: wifi만 활성화 여부 확인
  - on|off 를 생략하면 status를 보여줌
  
- WiFi가 disabled 되어 있으면 와이파이를 사용할 수 없으므로 blocked 되어있는지 확인하기
  - # rfkill list
  - soft blocked가 yes인 경우는 rfkill unblock [# | all]으로 해제 가능
  - hard blocked는 BIOS나 장치 firmware 기능에서 막힌 것이므로 불가
  
- soft blocked
  - 소프트웨어에서 wifi를 비활성화 시키는 기능
- hard blocked
  - embedded 되어 있는 wifi 칩을 BIOS에서 disabled 시킨 경우
  - 랩탑의 경우 FN + F5 같은 기능에 wifi를 on/off 할 수 있는데, 이 기능으로 인해 disabled 되면 hard blocked로 나타남
  - sleep 모드로 진입할 때 배터리를 절약하기 위해 이 기능이 기본적으로 켜지는 경우가 많음
  
- wicd vs nmcli
  - NetworkManager 사용 시 wicd는 사용할 수 없음(삭제하기)
  
- nmcli dev [list]: 장치에 wifi가 있는지 확인하기
  - list는 생략이 가능하지만 구버전에서는 list를 명시해야 하는 경우 있음
  
nmcli dev wifi

- dev wifi connect <SSID|BSSID> [password <pass>]
  - nmcli dev wifi connect Dev_wifi4 password qwer1234
  - nmcli d
  
- nmcli d disconnect wlan0

- WiFi hotspot(AP mode)
- nmcli c add type wifi ifname '*' con-name syhostspot autoconnect no ssid syhostpot_rpi
- nmcli c mode syhotspot 802-11-wireless.mode ap 802-11-wireless.bang bg ipv4.method shared ipv4.address 192.168.100.1/24
- nmcli c mod syhotspot 802-11-wireless-security.key-mgmt wpa-psk 802-11-wireless-security.psk suny1234
- nmcli c up syhotspot

- nmcli c s 으로 확인해보기

- nmcli -p c s syhotspot

- nmcli c down syhotspot

- nmcli c up syhotspot

- hostapd: AP로 구동을 위한 기능 제공 daemon
  - NetworManager보다 더 많은 기능 제공
  - 5GHz AP 구동 시 regulatory domain rule에 의해 region 설정 시 일부 채널이 블록되거나 passive로 작동 될 수 있음