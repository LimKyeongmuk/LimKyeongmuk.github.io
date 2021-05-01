---
title: "가상머신 소개 및 설치(Vmware)"
excerpt: ""

categories:
  - Linux
tags:
  - Linux
  - UNIX
  - 가상머신
  - Virtual machine
  - Ubuntu 설치
---
# 01. 가상 머신
### 가상 머신(Virtual machine)
- 현재 사용하는 OS 위에 다른 OS를 애플리케이션처럼 운용
  - 현재 운영체제: 호스트 운영체제(Host OS)
  - 가상 운영체제: 게스트 운영체제(Guest OS)
- 장점: 간편한 설치 및 구성, 백업, 삭제
- 단점: 가상화로 인한 느린속도, 일부 호스트 OS의 하드웨어 장치를 사용할 수 없음
4
### 가상 머신의 한계
- 성능 및 하드웨어 제한
- 일정 레벨 이상의 학습을 위해서 리얼 머신에 리눅스를 설치하는 것 추천
- 리얼 머신에서 리눅스를 직접 사용해야만 빠르게 학습 효과를 볼 수 있음
- 리얼 머신에 설치하는 경우, 실수로 인해 기존 하드 디스크의 내용이 모두 지워질 수 있음(기존 디스크 분리 또는 Back up하기)

### 가상 머신의 종류
- Full-Virtualization(전가상화)
  - 낮은 성능의 단점 vs 높은 독립성
  - 호스트 OS 위에서 하나의 애플리케이션으로 인식(해당 애플리케이션을 Hypervisor라고 지칭)
  - 가상화를 돕는 가속화 기능(optional)

  - 주로 사용되는 full-virtualization 종류
    - Windows: VMWare, Virtualbox(vbox), WSL(MS제공)
    - OSX: VMWare, Virtualbox(vbox), Parallels
    - Linux: VMWare, Virtualbox(vbox)
  - 라이선스: Virtualbox는 Opensource(Ext. pack 제외), 그 외는 상업적 소프트웨어

- Para-virtualization(반가상화)
  - 높은 성능 vs 낮은 독립성

### 가상 머신 다운로드
- VMware workstation player
- Ubuntu 18.04.05(카카오 미러 추천)

# 02. 가상 머신(VMware) 설치
1. VMWare 설치
  - User Experience Settings의 모든 옵션 disabled
2. License
  - 배우는 과정이기 때문에 Use VMware Workstation 16 Player for free for non-commercial use 선택
  - 화사에서는 사용하면 위험
3. VMWare 설치가 완료되면 Create a New Virtrual Machine으로 가상 머신 생성
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/1.PNG){: .align-center}
4. 바로 설치하는 위의 메뉴 대신에 "I will install the operation system later" 선택
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/2.PNG){: .align-center}
5. Guest operating system은 Linux에서 Ubuntu 64-bit 선택
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/3.PNG){: .align-center}
6. 가상 머신 파일들이 저장되는 위치, 파일 이름 선택하기
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/4.PNG){: .align-center}
7. 가상 머신이 사용할 디스크 크기 선택하기(Default값이 20GB이지만 사이즈를 눌려주는 것을 추천)
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/5.PNG){: .align-center}
8. 설정 확인 후 Finish를 눌러서 마무리
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/6.PNG){: .align-center}
9. 세부 설정을 수정하기 위해서 Edit virtual machine settings 실행
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/7.PNG){: .align-center}
10. Memory와 Process 개수 설정하고 "Virturalize Inter VT-x/EPT or AMD-V/RVI"을 반드시 체크해서 성능 속도 높여주기
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/9.PNG){: .align-center}
11. CD/DVD(SATA) 설정에서 "USE ISO image file"의 Browse 버튼을 눌러서 미리 다운로드 받은 Ubuntu 18.04 ISO 이미지 선택
12. 작업 완료 후 Play virtual machine 눌러서 시작
13. 한국어를 설정하고, Ubuntu 설치를 누르기
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/10.PNG){: .align-center}
14. 키보드 레이아웃에서 한국어가 설정되었는지 확인
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/11.PNG){: .align-center}
15. 가상 머신의 경우 서드 파이가 딱히 필요없으므로 선택하지 않아도 되지만 리얼 머신에서 선택해주면 좋음
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/12.PNG){: .align-center}
16. "디스크를 지우고 Ubuntu 설지" 누르기, 가상 머신의 디스크를 삭제하는 것이므로 원래 운영체제에는 영향을 주지 않음
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/13.PNG){: .align-center}
17. 시간대와 지역 선택(기본적으로 Seoul 선택)
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/14.PNG){: .align-center}
18. 모든 정보는 영문을 기준으로 적기(이름: 사용자 이름, 컴퓨터 이름: 호스트 이름, 사용자 이름 선택: ID)
   ![]({{site.url}/assests/images/2021-04-26-Linux-Basic-1/15.PNG){: .align-center}
19. 설치 완료 후 재시작(부팅이 완료되기 전에 클릭 및 화면 크기 및 설정 변경 주의)

# 03. 간단한 Ubuntu 설정
### 업그레이드 하지 않음
- 실습 환경이 18.04에 맞춰져 있기 때문에 업그레이드 하지 않음 선택
- 소프트웨어 업데이터가 뜨면 설정 → 업데이트 → 자동으로 업데이트 확인: 하지않기 / 새 우분투 버전 알려주기: 하지 않기로 변경
