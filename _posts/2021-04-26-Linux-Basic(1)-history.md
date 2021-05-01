---
title: "UNIX로부터 이어온 Linux 역사"
excerpt: ""
categories:
  - Linux
tags:
  - Linux
  - UNIX
  - Linux History
---

# 01. UNIX, Linux 역사를 배워야 하는 이유
### 1. 표준안(Standards)의 존재 의미를 알려줌
- 표준화된 지식과 규격은 호환성을 높임
- 효율적으로 협력 가능한 작업을 가능하게 함
- 과거 표준화 이전에는 혼돈

### 2. 지금도 표준화 이전의 세계가 존재함
- 무질서하고 국제 표준 및 업계 표준을 무시하면 시장에서 도태될 수 있음

### 3. 인터넷 검색에서 출처가 의심스러운 문서에 주의
- 각종 문서들은 틀린 정보들도 많은데, 초보일때는 분별하기 어려움
- 항상 **표준 문서**와 **공식 문서**를 먼저 보기

### 4. 인과 관계와 상관 관계를 이해할 수 있음
- 표준 규격이 나오게 된 결과와 원인을 알게 되면 현재 사용되는 기술이 어떻게 변해가는지 큰 숲을 볼 수 있음
- 단순 테크닉만 외우게 되면 초급 이상의 엔지니어(프로그래머)가 되기 힘들 수 있음

### 5. 기술의 발전은 어떤 **결핍**을 해결하기 위해서 만들어짐
- 기술적 결핍이 발생한 다음, 해결하는 과정에서 점진적으로 진행되다가 특이점에서 점프하게 됨
- 이런 과정 속에서 기존의 것을 향상시키거나, 아예 버리고 새로운 것을 만들기도 함
- 기술의 발전 방향을 예측하고 올바른 방향으로 예측하기 위해서는 역사적, 그 배경 속 인물들의 행동이 중요
- 기술뿐만 아니라 기술을 창조 및 개선한 사람의 행동도 중요

# 02.Linux의 조상 UNIX의 역사, 배경
### Multics Project
- GE, Bell Lab, MIT AI lab의 Mac 프로젝트 산물(1964)
- 목표: 운영체제의 복잡한 기능을 감추고, 시분할, 페이지/세그먼트 메모리 관리, 프로세스 관리, 파일 시스템, 주변장치 관리, 양방향 인터페이스 등등 다양한 기능을 실험하는 프로젝트
- Multics 프로젝트는 비대해지면서 GE, Bell Lab, MIT AI lab는 결별
- Multics Project의 일원이었던 Bell Lab의 **Ken Tompson**은 spin-off된 기술로 Unix를 작성(게임용)

### Ken, Unix and Games
- Ken Thompson은 게임을 위해서 Unix를 만들었음
- 게임을 빠르게 돌리기 위해서 Unix 운영체제를 만들음
- 그 이전에는 DEC 운영체제가 있었는데 Unix가 성공하면서 밀림
- https://www.bell-labs.com/usr/dmr/www/ken-games.html

# 03. C언어는 UNIX를 만드는 언어
### 새로운 프로그래밍 언어의 필요성
- 기존 Unix는 Assembly로 작성되어 새로운 CPU가 나올 때마다 거의 새로 작성되어야 했음(Asseombly는 CPU나 특정 아키텍처에 특화되어 있는 언어이기 때문)
- 원래 게임하려던 목적에 포팅에 너무 많은 시간이 걸리는 문제점
- Dennis Ritchie와 Brian Kernighan의 도움으로 B언어를 개량해서 C언어 개발(1973)

### Unix를 C언어로 작성하여 얻게된 장점
- 하드웨어가 달라져도 rebuild 혹은 약간의 수정만으로 포팅이 가능
- 그 결과 여러 기계에 빠르게 이식될 수 있게 됨
- 의도치 않게 어셈블리어의 몰락

### C Programmin Language 특징
- 고급언어이면서도 어셈블리어에 버금가는 성능
- 낮은 추상화 객체 지원(stdio, File 객체)
- 저수준의 하드웨어 조작 가능
- 쉬운 언어의 특성(당시에는 쉬웠음)

# 04. C Programming Language의 국제 표준: **ISO/IEC 9899**
### ANSI-C(C89)
- 1989년도 표준

### C99
- 1999년도 표준
- 현재 산업계의 **실질적 표준**

### C11
- 2011년도의 표준
- 많이 사용되지는 않음
  
# 05. UNIX의 양대산맥: SvsV, BSD와 호환성 문제
### AT&T Unix
- AT&T는 통신회사로 HW/SW 사업을 하지 않았음
- 교환기와 빌링 시스템 용으로 Unix 사용
- 그래서 실비 수준의 가격만 주면 Unix 제공

### AT&T에서 Berkeley로
- 1973년 SOSP(Symposium of Operation System Principles)
- Ken & Dennis의 Unix, C언어의 공식 발표에 참석했든 Berkeley 대학 측에서 관심을 가지게 되어서 Unix v4 소스코드를 받게 됨

### BSD(Berkeley Software Distribution) Unix
- Berkeley 대학에서 전달된 Unix는 인기가 계속 높아짐
- Ken Thompson이 안식년(1975)을 맞아 모교인 버클리의 객원 교수로 가게 되면서 Unix v6 포팅 성공
- 이 후 **BSD Unix**로 명명되어짐
- 이 때, C shell, 가상기억장치, **TCP/IP 네트워킹**이 도입
- BSD 계열은 실험적이고 미래지향적인 기능을 테스트하는 용도로 발전하게 되어서 상업적인 AT&T Unix와 차별성을 가지게 되는 계기
- Ken Thompson은 당시 대학원생인 Bill Joy(Sun Microsystems 창업자)를 만나면서 시너지 폭발

### 구심점의 부재
- 1970년대 AT&T의 Unix는 쉽게 소스 코드를 제공함
- 당시 Unix 소스 코드는 느슨한 제한의 라이선스로 대학에 배포
- 1970년대 후반, 대학에서 Unix를 사용했던 학생이 취직하면서 자연스럽게 업계로 전파(대학에서는 BSD가 많았기 때문에 추후 BSD 유닉스 계파를 탄탄하게 하는데 밑거름)

### BSD의 분위기
- BSD의 자유분방한 분위기와 반대로 Ken은 게임만 추구
- 인터넷이 발전되지 않아서 개발자들간의 소통 부재
- 폐쇄적 개발자들이 각자 필요한 기능만 만들어서 호환성 무너짐

### AT&T와 BSD 계열의 경쟁과 발전
| AT&T | BSD |
|:----:|:----:|
|UNIX version 7(1978)|1 BSD(1977)|
|SysV Release2(1984)|4.2 BSD(1984)|
|SysV Release3(1986)|**4.3 BSD**(1985)|
|**SysV Release4**(1984)|4.4 BSD(1993)|
- SysV Release4(SVR4)는 SysV의 표준(현대적 유닉스의 기준)
- BSD는 소송에 휘말리게 되서 4.3에서 많이 멈춤

# 06.POSIX: 최소한의 호환성 보장
### 호환성 파괴
- SysV 계열과 BSD 계열의 경쟁으로 호환성 파괴
- 1980년도 중반, 각 Unix 벤더들의 제품은 호환되지 않음(명령어 체계까지 다르게 됨)
- 각 Unix 벤더들마다 Unix를 경쟁적으로 customizing해서 제품과 통신 및 매뉴얼이 상이한 문제 발생
- 시스템 관리자들과 프로그래머들이 힘들어 함
- 이런 문제점은 **Java**의 탄생의 모티브

### 미 국무성에서의 강한 불만 표출
- 벤더들끼리의 스스로 표준화의 필요성이 대두됨
- 전기전자공학회(IEEE)의 주도로 System call의 표준화 진행
- **1988년 POSIX 초안 발표: POSIX.1 1003-1988**
- POSIX 규격에 맞추지 않으면 미정부 조달 불가

### POSIX(Portable Operating System Interface)
- Posix(파직스)는 Unix 시스템의 최소한의 호환성을 요구
- RMS(Richard M. Stallman)이 작명
- API의 문법과 작동에 대한 의미(Semantic)만을 담고 있음
- 세부적 구현 방법을 제약하지 않음(각 회사들이 표준을 지키지 않을까봐 느슨하게 작성)

### POSIX ver
- POSIX 1003.1의 System Call 표준안, 약칭: **POSIX.1**
- POSIX 1003.2의 System Interface 표준안, 약칭: **POSIX.2**(훗날, POSIX.1에 합쳐짐)
- POSIX 1003.1-1988: 초안
- POSIX 1003.1-1990: ISO 승인(IEEE std 1003.1-1988으로 불리기도 함)

### Minor vendors: OSF의 등장 배경
- 1980년대 Unix 업계는 AT&T, Sun(BSD)의 양강 구도
- POSIX는 정보 조달의 최소 기준이고, POSIX에 빠진 부분을 포함하는 더 넓은 의미의 산업계 표준(De facto)를 만들고자 AT&T, Sun의 공조
- AT&T, Sun microsystems는 업계 표준 Unix인 **SVR4**(1987 발표, 1988 공식릴리즈)를 만들어냄
- Sun microsystem은 자사의 Unix(SunOS - BSD)를 Solaris(SVR4 base)로 개작
- Minor vendor들이 자사의 UNIX가 SVR4에 의해 사장될 것을 두려워해서 방법을 모색하고 DEC가 총대를 매고 회의를 주최(DEC, IBM, HP 주축)

### OSF(Open Software Foundation)의 설립
- AT&T, Sun의 반독점을 목적으로 설립된 비영리 단체
- OSF를 만들어서 또 다른 표준안 제정(1988)
- 통합 유닉스로 OSF/1 발표(1992)
- 1990년대 초반에는 SVR4(Posix), BSD, OSF/1으로 family가 늘어남

### X/Open: 표준화된 매뉴얼의 등장
- X/Open은 1984년 유럽의 컴퓨터 제조업체들이 Open System의 표준화를 위해 출범된 단체
- 제조업자, 사업자 대표, 소프트웨어 벤더, 기관 등이 가입
- 유럽에서의 Unix는 **표준화의 제정이 아닌 교육과 퍼트리는데 주력함**(표준화를 위한 가이드라인 제시)
- 미국에서는 제품을 만드는데만 관심이 있어서 매뉴얼이 부족해지는 문제 발생
- X/Open의 가이드라인: **XPG**(X/Open Portability Guide)

### SysV 계열의 벤더도 X/Open에 가입
- SysV는 X/Open 진영의 수준 높은 가이드라인, 매뉴얼의 흥미를 가짐
- 우리가 현재 사용하는 **manual page**는 XPG의 산물(man 페이지의 하단을 보면 conforming to에 흔적이 남아있음)

### POSIX, SVR4, OSF의 관계
- POSIX는 최소한의 호환성을 보장: 공통 분모 역할
- POSIX의 공통 분모에는 optional한 부분이 있음(표준에 등재는 되어있지만 vender들이 꼭 구현할 필요는 없음)

### Posix로는 부족한 호환성: 단일 표준안의 필요성
- POSIX라는 국제 표준이 있음에도 불구하고 SysV, OSF/1, BSD, Old Unix 등등의 업계 표준이 있기 때문에 사용자들에게 혼란을 주는 경우가 많아짐
- XPG는 지침서일 뿐, 강제성이 없었음

# 07. 빌게이츠(Microsoft)의 등장으로 인한 SUS 출범 
### 단일 표준안의 필요성
- PC 시장에서 성공한 Microsoft사에서 Windows라는 운영체제 사용(기존에서는 MS-DOS)
- Windows를 기본으로 새로운 서버형 OS 제작 발표
- Unix 업계들이 패닉했고 OSF와 X/Open과 합병 (1996)

### 유닉스 단일 표준안: SUS(Single Unix Specification)
- SUSv1: XPG 4.2(Unix95) -> issue 4.20(420)
- **SUSv2: Unix98 -> issue 5(500)**(실질적 첫 SUS의 표준)
- SUSv3: SUS 2002 -> issue 6(600)
- SUSv4: SUS 2007 -> issue 7(700)

### Unix의 상표권
- 'AT&T'가 소유하던 것을 '노벨'에 넘겼다가 'X/Open'이 받음
- 'X/Open'의 합병 후 'Open Group'에서 소유

### Open System=Unix?
- 통합 후 Open Standard를 통해서 표준화(OpenGroup, IEEE, ISO/IEC에 의해 만들어진 표준 준수)
  - Open Group(http://www.opengroup.org/)
  - IEEE POSIX Standards(http://www.computer.org/)
  - ISO/IEC Standards(http://www.iso.ch/)
- Open System: 현대에서 API와 OS 구조가 공개되어 표준이 만들어진 시스템
- 넓게 보면 Linux까지 포함(여기서는 역사를 다루므로 "오픈시스템=Unix"라고 생각하기)

# 08. UNIX를 계승하지 않은 UNIX의 적통: Linux
### Linux Kernel
- Linux는 임시 운영체제이었음
- GNU 단체: MIT에서 시작된 해커들의 문화, OS는 독점되면 안된다는 의견
- GNU Hurd 커널(Micro kernel)의 개발이 지연되서 임시로 Linux kernel을 사용
- Linux에서는 kernel을 만드는 방법이 Micro kernel(New)와 Monolithic(Old)로 두가지(당시에는 Monolithic을 구식으로 생각함)

### Linux vs Minix
- Minix(Mini uNIX): 1987년 Prof. Andrew Tanenbaum이 교육용으로 작성한 OS
- 교육용이라서 OS 작동 방식만을 보여줄 뿐, 기능과 성능에는 제약이 많음
- '리누즈 토발즈'가 불편해서 다시 만든 것이 Linux kernel
- Torvalds vs Tanenbaum: 타넨바움은 리눅스의 Monolithic kernel은 obsolete kernel 방식이며 토발즈가 미래의  커널 방식인 Micro kernel을 사용하지 않음을 지적

### 리누스 토발즈(Linux의 아버지)
- Torvals라는 구심점이 존재했기 때문에 리눅스는 분열이 일어나지 않음
- 자비로운 종신 독재자(Benevolent Dictator for Life) 칭호 부여

### BSD, Linux, AT&T(SysV)
![]({{site.url}/assests/images/sysV&bsd.PNG){: .align-center}
출처: http://voyager8.blogspot.kr/2008/12/unix-genealogy.html

# 09. GNU와 Free Software Foundation
### GNU
- GNU: Gnu is Not Unix
- FSF의 Richard Stallman이 리딩하고 있는 단체
- 사실 Linux는 GNU의 컴파일러와 각종 유틸리티의 도움이 없었다면 발전이 불가능했기에 근본은 오히려 GNU에 있음

### Linux와 GNU의 관계
- GNU의 컴파일러 GCC에 의해서 더욱 발전
- GNU의 각종 툴 들이 포팅되어지면서 처음부터 POSIX 표준안에 근거하여 작성됨
- 비표준 GNU 확장이 있으나 오히려 표준안에 영향을 주기도 함
- Linux에서 커널을 제외한 나머지 부분은 GNU의 공로가 더 큼

### FSF & GNU & GPL
- FSF(Free Software Foundation): 소프트웨어의 자유로운 개발과 배포를 막는 기존의 상업적 지적재산권 제도가 SW 발전을 저해한다고 하여 설립됨(1985)
- GNU: FSF에서 만든 프로젝트
- GPL(GNU Public License) 공개 라이선스를 따르고 있음(우리 소스를 모두 보여줄테니까 너네 소스도 모두 보여줘라)
- 상업용에서는 제약이 적은 LGPL, MIT, BSD, Apache 라이선스를 사용하기도 함

# 10. BSD의 방계: Mac OS
### BSD UNIX 상황
- 회사가 아닌 대학에서 만들었고, 외부 코드에서 왔기 때문에 라이선스 분쟁 소송에 휘말림(UNIX 이름을 못쓰게 되고, 자금 지원이 끊김)
- 자금난으로 4.3 BSD의 개량 포기 -> 4.4 BSD Lite로 공개(불완전)
- BSD가 사라져가는 것을 계승하기 위해서 자발적 모임(FreeBSD, NetBSD, OpenBSD 등장)

### BSD와 Mac OSX(2001)
- 빌게이츠가 애플에서 쫓겨나고 NEXTSTEP이라는 회사 설립(전신이 BSD 시스템)
- 다시 빌게이츠가 애플에 들어가면서 NEXTSTEP을 가지고 들어가서 이것을 포팅해서 Mac OS X (Darwin) 만듬
- 오픈 시스템 특성인 포팅의 가능성이 열려 있어서 CPU 종속성이 적음

### Mac OSX와 Linux
- SUS 이후 BSD를 계승한 OSX는 당연히 SUS 표준을 대부분 만족함
- 생각보다 아주 최소 부분만 만족했기 때문에(애플에서 필요한 기능만 구현) 서버 시장쪽에서 채택이 잘 안됨
- Linux 기반은 Mac OS와 쉽게 포팅 가능

# 11. Linux의 성공 요인과 배포판: Red Hat, Debian
### Linux 성공 및 사이드 이펙트
- 소스 코드를 오픈
- Unix 표준(POSIX, SUS)를 받아들여 빠르게 시장 규모를 키워냄
- 1990~2000년대 IT 환경은 폐쇄적이었고 벤더들이 고압적인 태도 때문에 Linux의 발전

### 배포판
- 패키지 시스템: 프로그램이 미리 컴파일되서 실행 가능한 상태를 미리 묶어주고, 버전 관리를 시켜주는 시스템
- 어떤 형식으로 패키징하는 것에 따라서 계열이 나누어짐
- DEB 계열과 RPM 계열로 나뉘어짐
- 예전 Slackware에서는 발전이 더딤
- RedHat 사의 배포판은 RPM
- Debian은 GNU 사의 공식 배포판

### RedHat 계열(RPM 패키지 사용)
  - **RHEL**(RedHat Enterprise Linux)
  - Fedora
  - CentOS 

### RH 계열: version 차이
- CentOS쪽의 버전이 가장 낮은 경우가 많음
- IBM이 CentOS도 인수하고 CentOS를 없애고 CentOS Stream을 만들어서 배타 버전이 강한 것으로 낸다고 발표

### RH 계열: EPEL
- CentOS를 사용하는 경우 EPEL을 사용하는 경우가 많음
- EPEL(Extra Packages for Enterprise Linux)
   
### Debian 계열(Deb 패키지 사용)
  - **Debian** Linux
  - Ubuntu Linux
  - Kail Linux

### Debian 계열
- 수직 버전으로 차이가 발생하는 것이 아님
- 안정성을 중시
- Debian: 데비안 기본(작고 가벼운 시스템 구축이 목적)
- Ubuntu: 예쁘고 사용이 편리한 데스크탑 리눅스 구축이 목적인 것이
- 기본적인 내부구조는 둘이 비슷하지만 Ubuntu가 개조가 더 됌
- Debian은 패키지 방식이 좋지않아서 Ubuntu가 데비안과 결별한다는 얘기가 있음

### 배포판별 특징
- CentOS
  - 포털 및 스타트업 기업이 가장 많이 사용함
  - RHEL의 클론이라서 엔터프라이즈 환경에서 사용하기 편리
- Ubuntu
  - 화면이 아름답고 초보자가 쓰기 편리
  - 엔터프라이즈 환경에 사용하기가 불편하고 보안이 취약
  - 소형 기기에 최적화된 스핀오프가 많아 IoT embedded에서 많이 쓰임
  - 보안에 취약함
  - 스파이웨어나 상용 소프트웨어(뒷광고)를 포함해서 논란이 많음
- Fedora
  - RH 계열에서 가장 빠른 최신 기술을 확인하고 싶을때 많이 사용
  - 최신 기술을 확인하기 편리하여 선행기술 개발, 보안 시스템 개발에 쓰임

### Linux가 사용되는 곳
- Enterprise Linux
  - 대용량 웹 서비스
  - Cloud Service
  - 게임 서비스
  - 증권(Security trading)
- Embedded Linux
  - ARM 기반의 CPU
  - 라즈베리파이
  - 오렌지파이
  - 라떼판다
  - 오드로이드
- Gateworks(자동차, 자율주행)

## 12. Summary
### Multic로부터 UNIX 탄생(=Ken Thompson)
  - AT&T와 BSD로 갈라짐(원인과 결과 기억하기)
  - BSD에서 Sun microsystem 탄생
### 표준화: POSIX, X/OPEN, OSF/1의 관계
  - System V Release 4(SVR4, 1988)가 탄생 = 업계의 실질적 표준
### Windows NT로 인해 UNIX 업계의 통합 촉발
### SUS의 탄생(1~4)
  - XPG: man page는 SUS에 기초함
  - IEEE std 1003.1은 무엇인가 (POSIX 1003.1=POSIX.1)

## 13. Reference
### 공식 문서
  - Unix: www.opengroup.org
  - Linux 문서 및 교육 자료
    - Red Hat: access.redhat.com
    - Linux Foundation: www.linuxfoundation.org
### 추천 책, 만화, 영상(Unix, Linux 역사)
  - 책: 성낭과 시장 - Eric Raymod(Free PDF)
  - 책: **UNIX의 탄생** - Brain Kernighan
  - 만화: F/OSS cartoon - joone.net
  - 영상: Revloution OS(2001) - htttps:/youtu.be/4ZHloJVhcRY