---
title: "Linux Command(1) - Command"
excerpt: ""

categories:
  - Linux
tags:
  - UNIX
  - UNIX Command
  - Linux
  - Linux Command
---
# 01. Linux(Unix) Command
### Linux(UNIX) 필수 명령어
- CLI command: Command Line Interface 명령어(터미널 명령어)
- 필수 명령어는 수십 여개 정도 되지만 각 명령어마다 option들의 조합으로 매우 많음
- 자주 쓰는 명령어는 자연스럽게 외워지고, 그 외의 것들은 man 페이지를 보는 방향이 좋음

### Comand completion(명령행 완성 기능)
- auto-completion이라고 부름
- &#60;Tab&#62;키를 사용해서 prompt에서 파일, 디렉터리명의 일부만으로 나머지를 완성
- &#60;Tab&#62;키를 사용해서 prompt에서 명령어 다음에 치는 option, command를 자동 완성

### i18n(internationalization)
- i18n은 i~n 사이에 18개의 알파벳이 있음
- UTF-8이 기본문자세트로 사용됨
- 현재 Linux/UNIX command는 i18n에 맞춰서 만들어져 있음
- LANG 환경변수 설정의 영향을 받음(연습할 때는 en_US 로케일을 사용하는 편이 좋음)

# 02. Linux(UNIX) Basic commands
### 매뉴얼
- **man**
- whatis(==apropos): 검색 기능(구식 명령어)

### 파일관련
- path: **pwd**, **cd**, pushd/popd
- file data / meta-data:
  - 조회: **ls**, **file**, **stat**, tree, which, **find**, locate
  - 데이터 변경: **cp**, **mv**, **rm**, **mkdir/rmdir**, **ln**, readlink
  - 메타 변경: **chmod**, **chown**, chgrp, chattr/lsattr

### 파일 묶음
- archive: **tar**, cpio

### 압축
- compress: **gzip**, bzip2, xz, lz4, zstd

### Text 관련
- editor: **vim(vi)**, nano, emacs
- filter: **cat(tac)**, **head**, **tail**, **less** / more, **sort**, uniq
- regex: **grep**(egrep, fgrep), **sed**, **awk**

### Job control
- **jobs**, **fg**, **bg**, --nohup--, diswon

### Process control
- **kill**, **pkill**, **pgrep**, killall
- tracing: **strace**, pmap

### Networking
- **nc**(net cat), **curl**, **wget**
- w(who)

### Disk
- **df**(disk free)
- du(실제로는 사용하면 위험함)

### System
- uptime, **Free**(/proc/meinfo), smem
- process summary: **top**, htop, atop, sar, saidar
- process status: **ps**, pstree
- stat: **vmstat**, iostat, mpstat, **pidstat**, statgrab, dstat
- hardware: **lshw**, lspci, lsusb

# 03. Linux(UNIX) Admin commands
### Package
- RedHat: rpm, **yum**, dnf
- Debian: dpkg, apt-get/apt-cache/apt-file, **apt**, aptitude

### Network
- status: **ss**, --netstat--
- config: **nmcli**, / nmtui, ip, ifconfig/rout, iw/iwconfig
- arp, dig, --nslookup--
- ssh, sftp, scp, ssh-copy-id
- packet: tcpdump, wireshark, tshark

### Files
- open files: **lsof**, fuser

### Kernel
- 커널 파라미터: sysctl
- 커널 모듈: lsmod, modprobe, rmmod

### Firewall
- iptables, ip6tables
- filewall-cmd, ufw

### Disk
- **fdisk**, cfdisk, sfdisk, fixparts, flock,
- **parted**, gdisk, cgdisk, sgdisk
- **mkfs**, **fsck**
- **mount**, **lsblk**, **blkid**
- **grubby**, grub2-install, grub2-mkconfig, grub2-editenv
- lvs, vgs, pvs
- **udisksctl**

### Security
- **ulimit**
- visudo
- sestatus, getsebool/setsebool, getenforce/setenforce
- abrt

### User
- **useradd**, **groupadd**, **usermod**, groupdel, chgrp, newgrp
- **passwd**, **chpasswd**, gpasswd

### Service
- init, service, chkconfig, ntsysv, update-rc
- **systemctl**

### Performance
- turned-adm
- perf
- pcp

### Locale
- locale, localectl

### Alternatives
- update-alternatives

# 04. Linux(UNIX) dev.(개발자) commands
### Compiler
- gcc, g++, clang

### Debugging, tracing
- gdb, strace, coredumpctl

### Tools
- make, maven, graddle
- git

### python
- python2: pip
- python3: pip3