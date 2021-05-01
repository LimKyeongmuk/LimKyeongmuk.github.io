---
title: "VMware 설정"
excerpt: ""

categories:
  - Linux
tags:
  - Linux
  - UNIX
  - VMware
---
# 01. VT
### VT
- Virtualization Technology
- 가상화 가속 기능으로 활성화해야만 VMware가 빨라짐
### VT 활성화 확인 방법(Windows 10)
  > 1. cmd 혹은 powershell 실행
  > 2. systeminfo
  > Hyper-V 요구 사항
  > 펌웨어에 가상화 사용: 예
- VT 설정(1): BIOS 설정 수정
  - 펌웨어에 가상화 사용이 아니오인 경우 BIOS에 진입해서 변경
    - BIOS 진입 방법: 부팅시 delete 혹은 f1, f2, f10키로 진입
- VT 설정(2): VMware 소프트웨어의 VM 세팅의 Processor에서도 켜줘야 함
  > 1. Edit a Virtual Machine Settings
  > 2. Hardware
  > 3. Processer
  > - Virtualize Intel VT-x/EPT or AMD-V/RVI 체크(필수)
  > - Virtualize IOMMU(IO memory management unit)
    
- 주의 사항, Windows에서 WSL을 사용하면 Hyper-V와 충돌나기 때문에 제거해줘야 함
- VT 활성화에 도움이 되는 참고 영상: BlueStacks4 - PC에서 가상화(VT) 활성화하는 방법(https://youtu.be/XDeje9wjDp4)


# 02. Preferences 해두면 좋은것
  > 1. Player
  > 2. File
  > 3. Preference
  > - Suspend the virtual machine (체크 해제)
  > - Power off the virtual machine (체크)
  > - Check for product updates on startup (체크 해제)
  > - Check for software components as needed (체크 해제)
  > - Join the VMware Customer Experience Improvement Program