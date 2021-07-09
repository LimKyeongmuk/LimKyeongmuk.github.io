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
# FS
- FS(File System)
  - FS는 OS의 매우 큰 부부분 차지
    - 컴퓨터 시스템은 큰 틀에서 reade, evaluate, write의 작업을 진행하는데, 그 중 메모리의 역할 중 일부분을 FS가 담당
    - FS는 넓게 보면 데이터베이스의 일종
    - 파일(계층적 구조)를 저장
    - Linux: xfs, ext3
    - Windows: ntfs, exfat, fat32
  
  - File system type list
    - ext4: 리눅스 고유 파일 시스템
    - ext3, ext2: ext4의 이전 버전
    - xfs: 실리콘 그래픽스의 저널링 파일 시스템(RHEL7부터 기본 파일 시스템)
    - zfs: 솔라리스의 기본 파일 시스템(도입 예정, 아직 실험적)
    - Btrfs: zfs와 비슷한 컨셉으로 개발됨
    - ntfs: 윈도우즈 NT 계열에서 사용하는 파일 시스템
    - fat32: FAT 32비트 버전
    - exfat: FAT 32비트 확장 버전
    
  - 새로운 FS 만드는 법
    - Partitioning
      - command: fdisk, parted
    - File System(만들고 확인)
      - command: mkfs(=format), fsck or xfs_*
    - Mount(적재)
      - command: mount / unmount
      - /etc/fstab: 마운트를 자동으로 하기 위한 파일 시스템(tab은 table의 약자)
  
- Partition
  - Partition
    - Physical or Logical 구획
      - 과거에는 physical partition만 존재
      - 최근 modern OS에는 logical partition 선호
      - logical partition 중 대표적으로 Logical Volume(LVM)을 사용
      - logical partition을 사용하면 물리적 한계를 가진 디스크를 묶어서 보다 큰 용량의 파티션을 만들 수 있음
    - Physical disk를 나누기 위해서 disklabel이 필요한데 2가지가 있음
      - DOS 방식(혹은 msdos 방식)
        - 고전적인 방식(2TB 제한)
        - tool: fdisk를 사용함
        - Dos partition의 종류
          - Primary partition(주 파티션)
            - 한 디스크 당 4개의 파티션을 만들 수 있음(그 이상 X)
          - Extended partition(확장 파티션)
            - 4개 이상의 파티션이 필요할 때 primary partition 대신에 1개를 만들 수 있음
            - extended partition은 다시 여러 개의 logical drive로 나눌 수 있음
            - 결과적으로 4개 이상의 partition이 필요할 때 사용
          - Logical drive(논리 드라이브)
      - GPT 방식
        - dos 레이블의 문제를 개선하기 위해 나온 새로운 방식
        - 용량 제한이 없어짐
        - tool: parted / gparted of gdisk를 사용함
  
- fdisk: 파티션 만들기
  - fdisk -l: 파일 시스템의 리스트 출력
  - 최근에는 lsblk를 더 많이 사용
  - fdisk <block device>: 편집 모드
    - <block device>: 저장장치를 의미하는 용어
    - <character device>: 입출력 장치 의미(키보드, 마우스)
    - /dev/sd[abcd...]
      - sd가 붙으면 SCSI(스커지) disk
      - sd가 붙으면 Serial type: SATA, USB
    - /dev/hd[abcd...]
      - IDE disk(요즘 안씀)
  > # fdisk /dev/sda
  command에서 m 눌러보기
  - a: 부트 활성 플래그 지정(Dos, Windows 계열의 boot 드라이브 지정)
  - d: 파티션 삭제
  - l: 알려진 파티션 ID(다른 운영체제의 파티션 번호를 볼 때 사용)
  - n: 새로운 파티션 생성
  - p: 현재 파티션 상태를 출력
  - t: 파티션 ID(타입)을 변경
  - **q: 변경된 상태를 저장하지 않고 종료**
  - **w: 변경된 상태를 저장하고 종료**
  
  fdisk /dev/sda 하고 q로 종료(연습이기 때문에)

- 파일 시스템 만들기(Make FS)
  - 파일 시스템의 개념적 이해
    - 저장 장치(memory, disk)는 아주 커다란 종이로 비유하면 쉬움
  - Partitioning
    - 종이를 몇 개로 자르는 작업
  - Make FS
    - 파티션 된 종이를 A4와 같은 규격화된 형태로 잘라서 분철하는 것
    - 분철을 하면 권수와 페이지 번호가 만들어짐(인덱싱 가능)
    - 분철 방식, 밑줄, 칸의 간격 혹은 자물쇠를 설치하는 것이 FS type
  
- FS 별 특징
  - Linux에서 지원하는 FS는 여러 종류가 있는데
  - 크게 ext4(작은 기기에서 유리), xfs(고성능 PCIe SSD) 두 가지
  
- ext4
  - Extended File system 4
  - 대부분의 Linux에서 사용한 FS
  - Jounaling 지원(파일 시스템이 깨졌을때 굉장히 빠르게 복구)
  - 1EB의 volume 지원
  - 연속된 파일의 접근 및 작은 파일들의 접근이 빠른 장점
  - 저성능 I/O에서 효율이 높음(IoT 기기들)
  - 삭제된 파일을 복구할 수 있음
  
- xfs
  - 저널링 기반의 대용량 파일 시스템(64bit)
  - SGI 실리콘 그래픽사에서 개발, IRIX 유닉스에서 사용, 후에 오픈 소스로 이전
  - 2000년대 초반에 포팅되어 많은 기간 동안 테스트 됨
  - 대부분의 고성능 서버형 리눅스 배포판(Debain, RHEL7(CentOS 7)의 기본 파일 시스템으로 사용
  - Online 상태(mounted)에서 확장이 가능
  - 대용량 파일 처리 시 성능이 좋음(많은 개수의 작은 파일을 처리할 때도 나쁘지 않음)
  - kernel service (xfs*)가 관리
  - 삭제된 파일을 복구할 수 없음
  - 깨진 파일 시스템 복구를 위한 명령어로 xfs_repair 제공
  
- mkfs
  - make file system
  - 실습을 하기 전에 미리 empty partition을 만들어두기
  - 준비 과정
    - Disk 추가
      - VMware나 Virtualbox에서 Disk를 1개 더 추가
        Player > Manage > Virtual Machine Settings
        Add > Hard Disk > SCSI > Create a new virtual disk
        10G 이상 > Split virtual disk into multiple files > Hard disk 2 확인
        root 계정에서 바로 lsblk로 확인하면 virturl 머신이 보이지 않음 그럴 때 아래와 같은 명령어 쓰기
        ls /sys/class/scsi_host/ | while read hostdev ; do echo "- - -"> /sys/class/scsi_host/$hostdev/scan ; done
        lsblk로 확인
      - 최소 4G 이상의 디스크를 추가
    - Partitioning(fdisk)
      - Primary partition, 1G 1개
      - Primary partition, 2G 1개
      - 나머지는 빈 공간으로 두기
      - fdisk /dev/sdb > p(상태확인) > w(저장하고 나가기)
    
  - Make FS
    - mkfs [-t fstype] [options] <device>
    - mkfs.<fstype> [options] <device>
    - mkfs -t ext4 /dev/sdb2
    - mkfs.ext4 /dev/sdb2
  
  - FS Checking
    - fsck(file-system checking)
    - 일반적으로 offline된 FS을 체크(Online 상태의 FS를 검사하는 경우에는 안전을 보장할 수 없음)
    - fsck [-ARNV] [options] [-t fstype] <device>
    - fsck.<fstype> [-ARNV] [options] <device>
    - fsck -y -t ext4 /dev/sdb2
    - fsck.ext4 -y /dev/sdb2
  
  - fsck의 종료 코드를 보고 결과 확인(rc: return code)
    - echo $?: 바로 이전 명령어의 main() 함수의 리턴값
    - 0: 성공(나머지는 2의 비트 수로 sum of the following)
  
- 스왑 공간
  - 메모리가 부족할 때, 디스크에 메모리의 일부분을 백업받는 것(잠시 교체하는 것)
  - MS Windows에서는 pagefile이라고 부름(Paging의 결과로 swapping이 발생하기 때문)
  - swap in/out을 위해서
  - 실제로 RAM의 최대 2배까지 만들라고 하지만 요즘은 작게 만들음
  - mkswap [-L label] <device>
  - swapon -a
  - swapon <device | swapfile | -L label>
  - swapoff -a
  - swapoff <device | swapfile | -L lebel>
  - 스왑 파티션에 공간 작성
  # mkswap -L swapfs2 /dev/sdb1
  - 일반 파일을 스왑 공간으로 사용
  # dd if=/dev/zero of=.swapfile1 bs=1024 count=262144
  - 스왑 공간 활성/바활성
  # swapon /dev/sdb1
  # swapon ./swapfile1
  # swapon -L swapfs2
  - 스왑 공간의 확인
  # cat /proc/swaps
  - fstab에 등록된 스왑 공간 확인
  # grep swap /etc/fstab
  - 리눅스로 설치하는 프로그램은 가끔 bug가 있는 배포판이 있음
  - 한국어로 설치했을 때 Label 이름이 꺼져있을 수 있는데 label 이름을 변경해야함
  
# mount
  - mount
    - 파일 시스템의 탑재
    - root directory(/)를 기점으로 시작
    - mount: 마운트를 하는 명령
    - umount: 언마운트를 하는 명령
    - findmnt: 마운트 리스트 확인
  - 파일 시스템 2개가 있다고 가정
  - A를 root로 가정했을 때
  - A 아래의 usr을 B에 마운트
  - B의 bin dir이 usr/bin이 되어버림
  - 장점: 디렉터리의 용량이 다찼더라도 전체 시스템에는 문제를 주지 않음
  - 실습에서는 root에 몰아넣지만 실무에서는 파티션을 따로 나눠줘야 함
  
  - mount table 보기: mount
  - mount
  - mount [-t fstype] [-o option] [device] <directory>
    - fstype: ext4, xfs 등(실제로는 자동 판단 기능이 잘 되어있어서 자주 생략)
    - option: 공통: rw, ro, remount 등등
    - device: /dev/sda, /dev/sdb/, ... , /dev/dvdrom(device naming은 뒤에)
      - dev/sda: S-ATA, SCSI, SAS, USB 등 시리얼 블록 장치들 중에 첫번째 장치
        - dev/sda1: 1번째 장치의 첫번째 파티션
      - dev/sdb: S-ATA, SCSI, SAS, USB 등 시리얼 블록 장치들 중에 두번째 장치
      - dev/sr0: SCSI/S-ATA recorder 장치 중 첫번째 장치
      - dev/mapper/*: LVM 사용시 맵퍼로 맵핑된 디바이스
    - directory: 마운트 대상 지점(mount point)
  - umount <directory | device>
  - # ls /dev/disk
  - # ls -l /dev/disk/by-uuid

# Practice 
  > mkfs -t ext4 /dev/sdb2
  > mkdir /media/backup
  > mount -t ext4 /dev/sdb2 /media/backup
  > ls -l /media/backup
  > tar cfa /media/backup/etc_backup.tar.gz /etc
  > ls -l /media/backup
- umount
  > lsblk
  > cd /media/backup
  > pwd
  > umount /media/backup
  target is busy: 프로세스가 작동 중이므로 device를 해제할 수 없음
  이럴 경우 사용 중인 프로세스를 찾기 위해서 fuser <dir>로 해당 PID
  > cd ..
  > umount /dev/sbd2
  > lsblk
  > ls /media/backup
  > touch /media/backup/hey_there.txt
  > ls /media/backup
  > mount /dev/sdb2 /media/backup
  > ls <Alt-.>
  mount는 기존 디렉터리 위에 올라타는 것이기 때문에 기존 디렉터리에 정보가 있으면 보이지 않음
  umount로 해제하면 보이게 됌
  > findmnt
  > umount /dev/sdb2
  > mount -o ro /dev/sdb2 /media/backup
  > findmnt
  이것보다 더 편한 기능: mount -o remount,rw /dev/sdb2
  복구 모드를 사용할 때 rw 모드로 많이 사용
  
- USB memory
  - USB memory card/stick의 mount
  - USB 메모리를 장착하면 lsblk에서 장치명으로 인식
  - mount 명령으로 인식시키면 됨
  - FStype을 알고 있어야 하지만 자동 파악이 가능(정확하게 파악하려면 blkid 명령 사용)
  - VMware에서는 USB 2.0, 3.x에 따라서 설정을 바꿔야 함
  - USB가 3. ver이면 Virtual mauchine settings > Hardware > USB Contorller > Connections > USB compatibilit > 3.1로 변경
  
  - X window 데스크탑은 USB 연결시 자동 마운트로 연습불가
  - <Ctrl-Alt-F3>을 눌러서 console로 이동(tty3)
  - USB 연결하면 VMWare 팝업 창을 볼 수 있음
  - Connect to a virtual machine 선택
  - root로 로그인
  - 잠깐 Windows로 나가고 싶을 때에는 <Ctrl-Alt> 누르면 마우스가 잠깐 나옴
  - root로 로그인 후 lsblk로 확인해보기
  - # mkdir /media/usbstick
  - # mount /dev/sdc1 <Alt-.>
  - # lsblk
  - # blkid /dev/sdc1
  - # blikd /dev/sda1
  
  - # cp ~/.bashrc /media/usbstick/
  - # umount 하기
  - Disconnect 한 뒤에 hos(windows) 컴퓨터에서 .bashrc를 열어보기
  
- bind 기능: 디렉터리를 다른 위치의 디렉터리에 붙이는 기능
  - mount --bind /usr/src/redhad/RPMS /var/www/html/rpms
  - copy하면 비효율적이기 때문에 bind 기능 사용
  - 디렉터리의 일부를 특정 위치에 노출시킬 수 있음
  - 보통 ftp나 웹 서비스에서 다른 위치의 디렉터리를 노출시킬 때 편리
  
- Tip: Device is busy!
  - 마운트가 파일 시스템에서 프로세스가 실행중인 경우에 발생
  - 해결책
    - fuser로 해당 파일 시스템에서 구동 중인 프로세스를 체크/종료시켜야 함
    - fuser -c /media/cdrom
    - 해당 파일 시스템 아래에서 작동하는 프로세스를 체크
    - fuser -ck /media/cdrom
    - 해당 파일 시스템 아래에서 작동하는 프로세스를 체크하여 종료시킴 
  
- eject
  - 외부 장치를 빼냄
  - eject /media/cdrom
  - 수동으로 언마운트가 필요한 경우가 있으니까 안전하게 언마운트 해줌
  
# fstab
  - fstab: filesystem table
  - /etc/fstab 에 위치
  - 부팅 시 파일 시스템을 자동 마운트하기 위한 정보를 담고 있음
  - mount 명령을 단축시킬 수 있는 정보를 가짐: 6개의 필드로 구성
    - device(장치 파일)
    - mount point(마운트 포인트)
    - fstype(파일 시스템 타입)
    - options(옵션)
    - dump, fsck(덤프 여부, 부팅시 체크 순서(root만 1, 나머지는 2, 3, ...))
  - LVM vs FS(static vol)
    - CentOS(RHEL)
    - Ubuntu(debian)
  - fstab: 1번 항목: FS device 파일(mount할 device 장치(다양한 형태로 사용될 수 있음))
    - 1) /dev/sd*: 실제 디바이스 장치로 설정하는 방식(old)
      - serial device는 port를 순서대로 연결하지 않는 경우 장치명의 변경이나 역전될 가능성이 있음
      - port 0(sda), 3(sdb), 4(sdc)번 장치가 연결되어 있을 때, 새로운 장치 port 2번이 설치되면 우순선위가 (0(sda), 2(sdb), 3(sdc), 4(sde)로) 바뀔 수 있음
      - 이 문제를 해결하기 위해 Label, UUID 사용
    - 2) LABEL=name: 레이블 이름으로 검색하여 장치를 찾아내는 방식
      - LABEL: 식별 가능한 문자열을 FS의 label 부분에 넣어서 찾는 방법
      - disk를 설치할 때 error가 나서 다른 disk에 붙이면 같은 label 값을 가져서 중복될 수 있음
    - 3) UUID=uuid: UUID 값으로 검색해서 장치를 찾아내는 방식(new)
      - UUID(universally unique identifier)를 FS에 넣어 찾는 방법
      - 해시코드로 ramdom하게 만듬
      - 설치하는 프로그램마다 값이 다르기 때문에 오류가 나지 않음
  - fstab 4번 항목: 파일 시스템의 주요 옵션: mount의 옵션과 동일
    - defaults: 기본값 사용(기본값에선 부팅 시 자동으로 마운트 됨)
    - noauto: 부팅 시 자동을 마운트 하지 않음
    - rw: 읽기/쓰기 가능
    - ro: 읽기만 가능
    - user: 일반 유저도 마운트 가능
    - nouser: 일반 유저는 마운트 할 수 없음(수퍼유저만 가능)
    - noexec: 실행 파일의 사용 금지
  - Linux FS 관련 옵션(둘 중에 하나만 넣어주기)
    - relatime: atime을 특정 경우에만 업데이트
      - dir 탐색 혹은 잦은 읽기에 대해 성능 향상을 목적으로 함
      - atime 업데이트 조건을 느슨하게 함
    - noatime: atime 자체를 사용하지 않도록 함
      - log 및 매우 많은 depth를 가지는 디렉터리 및 파일 시스템에서 유리
  
  fstab: systemd
  - systemd가 사용되면서 *.mount를 관리하는 target에 의해서 fstab은 해석되어 unit화됨
  fs(ext4): LABEL, UUID 변경
    - e2label <device> [new-label]: ext4 리눅스 파일 시스템의 레이블을 확인 및 편집(설정)
    - tune2fs -U uuid_value <device>: ext4 파일 시스템의 다양한 설정 조정(설정)
      - U <clear|random|time|UUID_value>
        - clear: 기존의 것 삭제
        - random: random 값 생성해서 넣어줌
        - time: 시간 기반으로 생성해서 넣어줌
        - UUID_value: 직접 생성해서 넣어줌
          - uuidgen: UUID 생성 유틸
    - xfs_admin: XFS 파일 시스템의 레이블/UUID 확인 및 편집(label, UUID를 사용하는 방식이 오류를 방지할 수 있음)
    - findfs LABEL|UUID=label|uuid: 레이블을 검색
  
  # e2label /dev/sda2
  # e2label /dev/sda2 Myboot(Myboot로 이름 변경)
  # e1label /dev/sda2
  (실습 위험함)
  
  findfs를 이용한 LABEL로 Device 찾기: findfs LABEL=/boot
  
  sdb2를 fstab에 등록
    - 등록하면 장점
      - 부팅 시 자동 마운트 가능(물론 수동도 가능)
      - 수동 mount시 device 파일이나 mount point 중 1개만 입력해도 됨(mount /dev/sdb2 or mount /media/backup)
      - fstab 수정 시 오타가 나면 부팅 시 멈출 수 있음
      
    - vi로 /etc/fstab을 열고
    - mount /media/backup으로 명령하기
    - findmnt로 mount table 확인해보기
    - umount도 해보기
    - 실무에서 사용하는 LABEL 혹은 UUID 방식으로 수정해보기

  - LABEL 방식(요즘 잘 사용하지 않음)
  
  - UUID 방식(꼭 실습해보기)
    - blkid /dev/sdb2
    - tune2fs -U ramdom /dev/sdb2
    - blikd /dev/sdb2
    - /etc/fstb을 다시 수정하기 (UUID로)
    - mount /media/backup, umount /media/backip으로 여러번 확인해보기
  
- 디렉터리 구조
  - SUS 표준에 근거한 디렉토리 구조
    - /: root 디렉토리(root 유저의 홈디렉토리와는 다름)
    - /def: 장치 파일들(device file)
    - /tmp: 임시 파일 저장용 디렉토리(temporary directory)
    - /dev/null: 널 디바이스
    - /dev/console: 시스템 콘솔 장치
  - 리눅스의 추가적인 디렉토리 구조
    - /boot: 부팅을 위한 커널 관련 이미지
    - /bin: 기본 실행 명령어 파일들(바이너리, 실행파일)
      - /usr/bin을 가리키는 심볼릭 링크(RHEL 7)
    - /sbin: 수퍼유저용 시스템 관리용 명령어 파일들
      - /usr/sbin을 가리키는 심볼릭 링크(RHEL 7)
    - /etc: 시스템 설정 파일 및 관련 스크립트
    - /lib: 시스템 라이브러리
      - /usr/lib를 가리키는 심볼릭 링크(RHEL 7)
    - /lib64: 시스템 라이브러리 64bit 전용
      - /usr/lib64를 가리키는 심볼릭 링크(RHEL 7)
    - /usr: 응용 프로그램 관련 파일들(Windows: C:/Program Files와 비슷)
    - /usr/bin: 응용 프로그램 실행 파일들
    - /usr/sbin: 관리자용 응용 프로그램 실행 파일
    - /usr/lib: 라이브러리
    - /usr/lib64: 라이브러리 64bit 전용
    - 요즘은 응용 프로그램과 시스템 프로그램의 경계가 무너져서 /bin이 /usr 밑에 심볼릭 링크로 걸려있는 경우가 많음
    - /usr/include: C 언어용 헤더 파일
    - /usr/share: 응용 프로그램이 공유하는 파일(font, icon, man page 등)
    - /usr/src: 소스코드 (커널, 패키지)
    - /usr/local: 패키지(apt, yum)가 아닌 임의 설치(실제 compile 로 설치)되는 응용 프로그램 파일들(버전 관리가 잘 안됨)
    - /var: 각종 잡다한 파일들이 존재
    - /var/log: 로그 파일
    - /media: 마운트용 디렉토리 (구 버전에서는 /mnt)
  
  - 사용자들이 주로 만들어서 사용하는 디렉터리
    - /export: 외부 저장 장치(/exp)
    - /u00, /u01, ...: 오라클 DB 디렉터리
  
  디렉터리 구조의 변화
  - 최근 리눅스에서의 변화
  /usr 밑의 동명의 디렉터리에 심볼릭 링크화
    /bin, /sbin, /lib, /lib64
    
# proc filesystem
  - /proc
    - process information pseudo-filesystem
    - 프로세스 정보를 담고 있는 FS인척 하는 (가짜) 파일 시스템
    - 프로세스에 관련된 정보와 시스템 하드웨어, 커널 등 다양한 정보가 있음
    - 현재 running 상태의 정보가 있는데 PCB라는 중요한 정보도 가지고 있음
  - PCB(Process Control Block)
    - 프로세스를 관리하기 위한 메타 정보가 들어있음
    - 프로세스 PID, TIME, CPU 값들 등이 들어있어서 프로세스를 이해하고 관리하는데 중요
    - Process ID
    - Date, Time
    - Resorces info.
    - Authority, Priority
  - process
    - 프로세스는 fork(2) 호출로 생성되는데, 이 과정에서 복제를 수반함
    - UNIX-like 시스템에서는 First Process(PID1)를 제외하고 모두 fork로 생성
    - Process는 "실행 이미지"와 "메타 정보"로 구성
      - 실행 이미지: 순수한 프로세스의 실행 코드(기계어) 의미
      - 메타 정보: 프로세스를 관리하기 위한 정보를 의미
    - 모든 리소스는 실제 "data" 영역과 "meta info"가 pair로 저장
  
  - cd /proc 하고 ls 해보기
    - 푸른색 부분: directory(숫자인 이유 PID 번호이기 때문에)
    - 일반 파일이나 특정 영문으로 적혀 있는 것은 시스템에 관련된 정보
    - ls 결과 중에 하나 cd로 들어가서 ls 해보면 하늘색은 심볼릭 링크, 또 ls 하면 실제 위치 나옴
  - mount | grep proc하면 mount 되어 있음
  - 프로세스 정보 파일들
  
  |파일|설명|
  |:---|:---|
  |cmdline|프로세스가 실행될 때 사용된 명령어 행(인수 포함)|
  |cwd|프로세스가 실행되고 있는 디렉터리|
  |environ|프로세스가 가지고 있는 환경변수 목록|
  |exe|실행 파일의 위치(심볼릭 링크로 가리키고 있음)|
  |fd|열려진 파일 목록을 담고 있는 디렉터리|
  |mem|메모리 사용량|
  |smaps|공유 라이브러리 사용 목록 및 각종 메모리 점유 정보|
  |status|프로세스 상태 정보|

  - ls -l /proc/####/fd하면 모두 심볼릭 링크
    - 0(stdin), 1(stdout), 2(stderr)들이 있는데 null이면 사용하지 않는다는 말
    - 실무에서는 직접 proc 파티션을 보는 방법보다 lsof -p 3751090 명령으로 확인
    - 간호그 TCP socket을 close 하지 않아서 CLOSE_WAIT 걸인 시스템이 잔뜩한 서버는 위 목록이 나오지 않음
  
  /proc에 있는 시스템 정보
  
  |||
  |:---|:---|
  |cpuinfo|CPU에 대한 정보|
  |diskstats|시스템의 디스크들에 대한 정보|
  |interrupts|시스템의 IRQ에 대한 정보|
  |**meminfo**|시스템 메모리에 대한 정보|
  |net|네트워크에 관련된 정보 및 설정을 포함하는 디렉터리|
  |partitions|시스템 파티션에 대한 정보|
  |**stat**|CPU statistics|
  |swaps|스왑 블록(파티션)에 대한 정보|
  |sys|시스템 설정(커널 설정을 포함)을 포함하는 디렉터리|
  |**loadavg**|Load average + sched. entity(Running/Total) + LastPid|

  - cat /proc/cpuinfo: cpu의 스펙 정보
    - processor: 프로세스의 개수(19번까지 있으면 20개)
    - vendor_id: 회사
    - cpu family: 6이면 686
    - model: 85번째 모델
    - siblings: 코어 옆에 붙어 있는 형제 CPU(가상 CPU까지 포함)
    - cpu cores: 실제 물리적 cpu 개수
    - bugs: 어떤 버그들이 패치되어있는지 보여줌
    - cache_alignment: cache size(제일 빠른 메모리의 단위 사이즈)
    
  - # cat /proc/stat: CPU와 process 관련 글로벌 통계 정보
    - btime: boot time 
    - processes
    - procs_running
    - procs_blocked: hw가 과부하 걸렸을 때 늘어남
  
# udisks2
  - udisks2
    - D-bus 기반의 block device manager
    - daemon service 작동
    - 다양한 block device(HDD, USB, ...)에 대한 관리 가능
    - udisksctl CLI 제공
    - 일반 유저로 명령할 수 있음(sudo로 X)
  - 시스템에 서비스가 설치되어있는지 확인: systemd unit: udisks2.service
    > $ systemctl status udisks2  (서비스를 조회하는 기능)
    
  - 콘솔이 아니라 X window 데스크탑 사용 시 기본이 disalbed이지만 active 될 가능성이 있을 수 있음(GNOME 환경이 start)
  - 큰 상관없음
  - udisksctl
    - command completion이 지원되므로 <tab> 두번 쳐보기
    - udiskctl mount --b<tab>
    - udiskctl mount --block-device /d<tab>
    - udiskctl mount --block-device /dev/<tab><tab> 으로 명령 확인
  
  - usb memory stick의 mount를 console에서 진행해보기(x Windows에서는 꽂는 순간 자동 mount)
    - lsblk
    - udisksctl mount --block-device /dev/sdc1
    - lsblk
  - unmount 해주기
    - udisksctl umount --block-device /dev/sdc1
    - lsblk
  - loopback device 설정
    - 파일을 device 처럼 mount할 수 있음(주로 ISO DVD image 마운트)
    - ls -l * iso
    - udisksctl loop-setup --file ubuntu-16.04.7-server-amd64.iso
  - loopback device 해제
    - udisksctl umount -b /dev/loop0p1
  - dump 명령으로 device 들을 살펴 볼 수 있음
    - udisksctl dump
  - 개별 장치 정보 보기
    - udisksctl info -b /dev/sdc1
  - 현재 시스템의 어디에 매핑되e  - systemd: background
  - init
    - UNIX-like 시스템에서 PID 1(Ancestor process)은 매우 중요
    - 모든 프로세스의 조상
    - 모든 프로세스는 parent process로 부터 복제(fork)됨
    - PID 1 프로세스만 복제가 아닌 kernel에 의해 생성됨
    - PID 1 프로세스는 시스템 기동 및 관련된 작업을 하는 최초의 프로세스
  - SysV init
    - 상업용 유닉스(AT&T), 그리고 이후 유닉스 혈통을 잇는 SysV 계통에서 표준화
    - 그 후 UNIX-like 시스템에서 오랫동안 init가 PID 1의 자리를 이었음
    - Linux도 SysV init를 채택
    - 그러나 2011년부터 systemd로 대체
  - init 프로세스란? 
    - SysV 유닉스에서 만들어지
      - Ancestor process: PID 1번
    - 부팅 시 초기화를 담당
      - /etc/inittab을 사용
      - 초기화 후 runlevel 기능에게 제어를 분할함
      - init와 runlevel(rc system) 같이 사용되는 경우가 많았음
    - 장점
      - 초기 SysV 유닉스 시스템부터 일관된 형태를 유지함
      - 느슨한 결합(기능이 적고 자유도가 높음)
    - 단점
      - 기능이 없어도 너무 없음
      - 하위 서비스들의 제어 및 레포팅이 제대로 되지 않음
      - 병렬 처리가 되지 않음(부트 및 종료 시간이 직렬화 처리되어 느림)
  - upstart
    - Ubuntu 시스템에서 채용
    - init 모든 기능은 그대로 둔 채 runlevel 관련 기능을 대체
    - event-driven 방식으로 작동하여, 서비스의 dependency를 해결
    - 초기에는 Fedora Linux에 도입되어 테스트 됨
    - 예상보다 부팅 속도가 개선되지 않았고 여러가지 문제가 있었음
    - 단점
      - 공용 서비스는 dependency에 의해서 복잡하게 꼬일 수 있음
      - 모니터링은 여전히 이상 작동하는 경우가 있음
  - systemd
    - 시스템 설정을 통합 관리하는 데몬
    - Lennart Poettering이 개발(오디오 관련 부분을 개발하던 개발자)
    - 시스템, 서비스, 자원 설정, 이벤트를 통합 관리
      - 데몬과 제어 통신 프로그램으로 분리되어 제작됨
      - D-BUS 사용(제어를 위해 향상된 IPC, 원격 기능 제공 기능)
      - loginctl, journalctl, systemctl 등을 제공함
      - 시스템의 기동, 종료(poweroff, halt)도 모두 통합 제어
    - Fedora 15부터 upstart를 대체함
    - 대부분 systemd 사용
    - 반론: UNIX, Linux 철학을 위배하고 있다고 지적함
    
  - SysV와 일부 호환 명령 제공(SysV 방식은 사용하지 않기): chkconfig
  - 호환성을 위해 systemd에서 제공되는 명령어
    - halt, init, poweroff, reboot, runlevel, shutdown, telinit 명령은 호환성을 위해서 심볼릭 링크로 가리킴
  
  - unit 특징
    - unit: systemd에서 관리하는 단위
      - sevice unit, target unit, device unit, mount unit, timer unit...
    - 동적 상태
      - unit의 현재 상태가 동적으로 관리되므로 외부 명령어에 의존하지 않음
      - notification이 가능하므로 이벤트 처리가 가능함
    - 병렬 처리
      - 부팅 혹인 target 진입시 선, 후 관계에 의해 프로세스를 병렬로 실행
    - 똑똑해진 종료
      - shutdown시 실행 중인 유닛만 중지하고 빠르게 종료
      - 기존의 init 기반에서는 기ㅖㄲ적으로 심볼릭 링크에 연결된 종료 스크립트 작동
    
  - 실행파일 3가지 카테고리
    - system coonfiguration
      - hostnamectl, localctl, timedatectl, machinectl
    - system monitoring/querying
      - system-analyze, journalctl, loginctl
    - system cotroolling
      - systemctl
    
  - systemd는 SysV init script와 일부 호환성을 제공함
    - /etc/init.d
  
  - systemd
    - unit 단위로 관리
    - unit 종류 확인하기
      - systemctl --type help 
      - systemctl -t help
      - systemctl -t <unit>
  
  - systemd의 unit file directory 위치 읽어오기
    - pkg-config systemd --variable=systemdsystemunitdir
  - systemd의 config directory 위치 읽어오기
    - pkg-config systemd --variable=systemdsystemconfigdir
  - ls 명령으로 해당 위치 읽어올 수 있음
    - ls $ (pkg-config systemd --variable=systemdsystemconfigdir) | head
  - cat 명령으로 파일 읽을 수 있음
    - cat $ (pkg-config systemd --variable=systemdsystemconfigdir) /NetworkManager.service
    - systemctl cat NetworkManager[.service] 사용하면 더 간단하게 파일을 읽을 수 있음
  - systemd의 global config. directory를 보기
    - ls /etc/systemd/system/multi-user.target.wants/
    - ls /etc/system/system/graphical.target.wants/
  
  - systemd에서 관리되는 주요 unit 항목들
  
  |||
  |:---|:---|
  |*.device|시스템 장치|
  |*.mount|파일 시스템 마운트 포인트| 
  |*.automount|파일 시스템 오토마운트 포인트|
  |*.path|시스템에서 감시하는 경로|
  |***.service**|시스템 서비스|
  |***.slice**|슬라이스(다른 유닛들에게 사용되는 일부분)|
  |*.socket|시스템에서 생성하는 소켓|
  |*.swap|스왑 공간|
  |***.target**|unit의 논리적 그룹(runlevel을 대체하는 유닛)|
  |***.timer***|타이머|
  |*.snapshot|스냅샷(유저가 설정한 target)|

# systemctl #1
  - systemctl
    - systemd를 관리하는 매니저 프로그램
    - service 및 resource의 상태 질의
    - service control
    - service unit의 동작 및 설정 변경 가능
  - systemctl [command] [options ...]
    - default command: list-units
    - 인수없이 systemctl 단독으로 쓰일 경우 loaded unit 리스트를 확인하는 기능인 list-unit 명령이 작동
  - systemctl [-t unittype] [-a] [--state=states] [-l]
    - t unittype, --type=unittype: unittype에 해당하는 unit만 출력
    - a, --all: load 되지 않은 유닛들도 모두 출력
    - --states=states: 조건을 지정하여 출력할 유짓 지정
    - -l, --full: 출력 항목의 내용을 줄이지 않음
  
  - service 유닛의 목록을 확인해보자
    - systemctl(종료는 q)
    - systemctl -t service: service만 출력
    - pager(=less)로 넘어가기 때문에 검색 기능 가능
      - /를 누르고 NetworkManager를 검색, i를 누르지 않으면 대소문자를 가리지 않음(toggle 명령어)
      - 오른쪽 화살표를 누르면 뒷 부분까지 보여줌
    - systemctl -t service --no-pager: pager를 사용하지 않고 보여줌
  - Header 부분의 LOAD, ACTIVE, SUB
    - LOAD: 해당 유닛의 설정이 메모리에 올라갔는지 알려줌
    - ACTIVE: 해당 유닛이 active이면 성공적으로 시작한 것 의미(성공/실패 정도만)
    - SUB: ACTIVE 항목의 low-level 작동을 의미(성공 시 어떤 작업을 실행했는지 알려줌)
    - LOAD, ACTIVE, SUB 칼럼의 상태 표시
  
    |Category|Value|Description|
    |LOAD|loaded|메모리로 로딩된 유닛|
    |LOAD|not-found|유닛을 찾을 수 없음(로딩되지 않음)|
    |ACTIVE|**active**|동작 중인 유닛(enabled)|
    |ACTIVE|in-active|동작하지 않는 유닛(disabled)|
    |SUB|**running**|프로세스가 작동 중(daemon)|
    |SUB|**exited**|프로세스가 종료되었으며 성공 혹은 실패의 종료 값을 리턴(1회성인 프로세스가 있을 수 있음)|
    |SUB|dead|프로세스가 알 수 없는 상태로 종료됨|
    |SUB|waiting|다른 유닛의 이벤트를 기다리는 중|
    |SUB|listening|소켓이 리스닝 상태|
    |SUB|mounted|파일 시스템이 마운트된 상태|
    
  - service 유닛의 상태로 조건을 사용하자
    - --state=<state>
    - systemctl --state=dead
    - systemctl --all -state=dead
  
   - l or --full: 전체 화면을 길게 보여줌

- systemctl <command> <unitname> [.service]
  - unit의 status, start/stop 등 다양한 명령을 내릴 수 있음
  - status는 일반 유저도 볼 수 있으나, start, stop, kill 등의 시스템 서비스에 영향을 주는 행위는 수퍼 유저만 가능(혹은 sudo 허가된 유저)
  - pager를 비워두면 한 번에 실행
  
# systemctl#2
  - systemctl <command> [arg...]
    - status [NAME...]: 유닛의 상태 출력
    - start [NAME...]: 유닛을 시작함(유닛을 active 상태로 만들음)
    - stop [NAME...]: 유닛을 정지함(유닛을 incative 상태로 만들음)
    - is-active [NAME...]: 유닛의 active 상태를 출력함(active인 경우 exit code:0 , 그 외에는 non-zero로 세팅)
    - is-failed [NAME...]: 유닛의 failed 상태를 출력함(failed인 경우 exit code:0 , 그 외에는 non-zero로 세팅)
    - kill [NAME...]: 시그널을 보냄(--kill-who<ID>, --signal=<sig. number>을 사용할 수 있음)
    - list-units: 유닛 리스트 출력. default command이므로 직접 사용하지는 않음
  
  - 미리 nginx 설치하기
  - systemctl status nginx
  - systemctl is-cative nginx
  - echo $?
  
  - systemctl status NetworkManager

  - systemctl start nginx
  - systemctl status nginx
  
  - systemctl is-active nginx
  - echo $?
  - systemctl is-failed nginx
  - echo $?
  
  - 한번 failed 되어있는 경우에는 failed unit은 재시작할 수 없음
  - systemctl reset-failed

  - systemctl kill nginx --signal=SIGABRT

  - systemctl <command> [arg...]
    - reload
      - 유닛의 서비스 설정을 리로드
      - 작동중인 PID 및 static resources가 유지됨
      - reload가 지원되지 않는 서비스도 있음
    - restart
      - 서비스를 재시작함
      - 정지된 서비스인 경우에는 start로 작동함
    - **try-restart**
      - 서비스를 재시작하려고 시도함
      - 정지된 서비스인 경우에는 아무일도 하지 않음
    - systemd unit 등 설정 파일이 바뀌면 항상 **daemon-reload** 해주기
    
# system #3
# systemctl unit 부팅시 자동 실행
  - systemctl <command>
    - is-enaable [NAME ...]: 유닛의 enabled 상태 확인(enabled, linked, masked, static, disabled)
      - enable(0): enabled 상태. 부팅시 자동으로 실행됨
      - linked(1): 기본 유닛 디렉터리가 아닌 곳에 있는 유닛 설정 파일로 symlink 파일이 systemd config dir.에 만들어진 경우
      - masked(1): 유닛 symlik가 null과 연결되어 실행 금지된 상태
      - static(0): 항상 실행되는 상태(다른 유닛의 dependency에 의해 실행되는 경우 의미)
      - disalbed(1): 유닛이 enabled 되지 않은 상태
    - disalble [NAME ...]: 유닛을 비활성화
    - enable [NAME ...]: 유닛을 활성화(**--now 옵션 사용 시 enable & start로 실행**)
    - preset [NAME ...]: 미리 설정된 기본값으로 설정
    - reenable [NAME ...]: 유닛 파일을 재활성화(기존 설정을 지우고 새로 활성화)
    - --runtime 옵션을 추가하면 설정은 변경되지 않고 임시로 메모리에서만 작업
    
    - systemctl enable nginx
    - 수동으로 심볼릭 링크를 만드는 방법은 좋은 방법이 아님
    - systemctl is-enabled nginx
    - systemctl staitus nginx
    - systemctl reenable nginx(disable > enable)
    - systemctl preset nginx
    - system is-enabled nginx
    - systemctl enable nginx
    - reboot 하면서 자동으로 active 상태가 되는지 확인
  
    - systemctl is-enabled dev-hubepages.mount
    - systemctl status dev-hugepages.mount
  
# target 설정
  - target unit
    - 유닛들을 논리적으로 묶은 그룹
    - 시스템이 시작한 이후에 단계별로 기능을 묶음
    - 1단계 시스템 초기화, 2단계 네트워크 초기화, 3단계 데스크탑 시스템 시작 등
    - 과거 SysV runlevel의 기능을 대체하고, 더 체계화됨
  - SysV runlevel
    - systemd 이전의 SysV에서 시스템의 상태 레벨 정의
    - 0: halt, 1: single user mode(root로만=복구모드), 2:multi user mode without NFS
    - 3: multi user mode with full networking
    - 4: reserved, 5:X11 desktop manager, 6:reboot
    - 문제점
      - 정적 상태를 기준으로 함
      - runlevel 교체시 의미없는 재시작되는 서비스들이 생김
      - 스크립트의 동작 순서가 serial 하게 작동함
      - runlevel의 일부 기능을 그룹화할 수 없음
    - systemd target
      - 동적 상태를 기준으로 함(이벤트 기능 가능)
      - 동작 순서를 parallel하게 작동시킴(의존성과 현재 작동죽인 동적 상태에 따라서 실행 순서 생략 및 변경 가능)
  
    - systemd target의 종류 보기
      - systemctl -t target
    
    - runlevel을 대체하는 타겟
      - poweroff.target: 시스템 끄기. runlevel 0 호환 모드
      - rescue.target: 복구 모드. runlevel 1 호환 모드
      - **multi-user.target**: 멀티 유저 모드(runlevel 2, 3, 4)
      - **graphical.target**: 그래피컬 모드(X 윈도우, runlevel5)
      - reboot.target: 리부팅. runlevel 6
      - shutdown.target: runlevel 0
      - halt.target: 시스템 끄기(=shutdown.target)
      - emergency.target: 응급용 콘솔(부팅 시 systemd.unit=emergency.target으로 사용 가능)
    
    - 그 외의 논리적 타겟들
      - local-fs.target
      - remote-fs.taraget
      - getty.target
      - basic.target
      - network.target
      - sound.target
      - sysinit.target
      - nfs-client.target
  
    - systemctl isolate <target>: <target>을 switching 하는 기능
      - systemctl isolate graphical.target
      - systemctl isolate multi-user.target
        - multi-user로 스위칭하면 순간 xwindow가 꺼짐 
      - systemctl isolate power.target(x) → poweroff
  
    - default target
      - 부팅할 때 기본으로 어떤 타겟을 설정
      - systemctl set-default graphical.target: x window 로그인 환경 사용
      - systemctl set-default multi-user.target:: Full networking 로그인 환경 사용(=Text User Interface)
        - 서버의 경우 많이 쓰임
      - systemctl get-default: 현재 default target 확인
  
    - systemctl isolate multi-user.target
    - systemctl set-default multi-user.target
  
  - systemd-analyzer
    - systemd의 구동을 분석(각 서비스의 시간을 분석 및 시각화)
    - command
      - blame: 서비스 작동에 걸린 시간별로 출력
      - **critical-chain**: 크리티컬 체인을 출력함
      - plot, dot: 부팅 과정을 SVG 그래픽 파일로 출력
      - dump: 시스템 정보 덤프
      - verify: unit 파일 작성/변경 후 검증
  
    - systemd-analyze: 부팅 시 얼마나 걸렸는지 알려줌
      - kernel, inited, userspace 
    - systemd-analyze blame: 서비스 별로 작업 시간을 알 수 있음(오래 걸린 서비스나 문제가 있는 서비스를 알 수 있음)
    - systemd-analyze critical-chain: critical path 형태로 출력
    - systemd-analyze plot > svgfile.svg: 그래픽컬 하게 분석해줌
    - systemd-analyze dum: 현재 서버 상태 덤프해줌
  
  - loglevel
    - default log level
    - # grep -i loglevel /etc/systemd/system.conf
    - change log level
      - systemd-analyze set-log-level <loglevel>
      - loglevel = <emeg|alert|crit|err|warning|notice|info|debug>
      - systemctl -p LogLevel show
      - systemd-analyze set-log-level warning
      - systemctl -p LogLevel show
  
# journald
  - systemd의 로그 관리를 위해서 사용되는 daemon
  - kernel과 userland 메시지를 통합 관리
  - 따라서 전통적인 syslog 데몬으로부터 정보 수집이 포함됨
  - binary data로 인덱싱을 하므로 빠른 검색과 출력이 가능함
  - runtime시에 journal이 쌓이기 때문에 /run/log/journal 디렉터리를 가짐

  - journalctl [options]
    - **-e**: (end)출력시 저널의 마지막 행으로 점프(최근 저널을 볼 때 편리, -x와 같이 쓰이는 경우기 많음)
    - **-f**: (following 모드) 맨 끝 저널부터 시작하며 추가되는 부분을 출력
    - -n [lines]: 해당 라인 수만큼 출력
    - -a 출력이 불가능한 문자 필드까지 모두 포함
    - **-u <unit>**: 특정 유닛에 대한 로그 출력
    - <binary path>: 특정 바이너리에 대한 로그를 출력
    - **-x**: catalog를 출력함. 이는 저널에 대한 상세한 설명 추가, 주로 에러 메시지 분석이 필요한 경우에 사용
    - **-o <format>**: 출력 포맷 지정(systemd 버전마다 다를 수 있음)
      - short: 간단한 로그형식
      - json: JSON 방식 저널
    - **--since=<DT>**: DT(datatime)에 지정된 시각 이후부터 출력
    - --until=<DT>: DT에 지정된 시각 이전까지를 출력
    - **-r**: 역순으로 출력
    - **-p <priority>**: 특정 로그 우선 순위를 지정(syslog에서 사용하는 레벨을 그대로 사용 가능)
    - --list-boots: 부트 ID
    - --disk-usage: journal log directory가 차지하는 용량 확인
    - --vacuum-size: journal log 삭제, size 기준(suffix: K, M, G, T)
    - --vacuum-time: journal log 삭제, 시간 기준(suffix: s, min, h, days, months, weeks)
  
  - journalctl: 로그 출력
  - journalctl -o short: 간략한 로그 출력
  - journalctl -o json: json 규격에 맞춰서 출력
  - journalctl -o verbose: 자세한 정보 출력
  - journalctl -e: 마지막 부분으로 자동 점프
  - journalctl -o short -n: 최근 10개 저널 표시
  - journalctl -o short -n --no-pager: pager 사용하지 않고 최근 10개 표시
  - journalctl -o short -n 50 --no-pager: 기본으로 최근 50개 저널 표시
  - journalctl -f: tail -f logfile 방식처럼 following log messages
  - journalctl -u unitname : 특정 unit을 검색
  - journalctl -k: kernel messages 출력(=dmesg -T)
  - journalctl -k -b 1: 디스크에 기록을 하는 경우라면 이전 기록의 커널 메시지 출력
  - journal은 기본적으로 메모리 기반으로 저장되는 방식이기 때문에 메모리에 저장하는 것을 디스크에 저장해야 영구적으로 저장 가능
    서버는 계속 운영하기 때문에 저널을 계속 사용하지만 개인 PC는 종종 종료하기때문에 디스크에 저장하는 기능이 있음
    - mkdir /var/log/journal
    - chown root:systemd-journal !$
    - chmod g+s !$
    - systmectl restart system-journald
  - journald config: /etc/systemd/journald.conf
     cat /etc/systemd/journald.cof
    jounal이 차지하는 용량 확인 및 삭제
      jounalctl --disk-usage
      journalctl --vacuum-size=30M
      journalctl --disk-usage
    
  - 에러만 보고 싶다면
  journalctl -p <priority>
    - priority: system log level
    - loglevel
  
  - 30분 이내에 일어났던 일만 보고싶을때
  journalctl -r --since -30m
  journalctl -rp warning --since -1h
  journalctl -p err --since today
    
  - 최근 사건에 대해 자세히 리포팅이 필요할 때
    journalctl -xe
    
  - 10분 이내에 일어났던 에러에 대해 자세히 리포팅
    jounalctl -xe -p err --since -10m
    
  연습