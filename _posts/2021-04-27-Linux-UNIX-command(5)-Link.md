---
title: "Linux Command(5) - Link"
excerpt: ""

categories:
  - Linux
tags:
  - UNIX
  - UNIX Command
  - Linux
  - Linux Command
---
# 01. Link: hard link, symbolic link
### Link의 개념
- ln: make links

### 하드 링크(hard link): 파일명을 통해서 i-node를 접근
- 같은 i-node를 가리키므로 동일 파티션(volume)에서만 생성 가능
- regular file만 가능(directory나 device file은 hard link 생성할 수 없음)
- 실체를 가진 파일

### 심볼릭 링크(symbolic link): 완전히 다른 파일로 단지 특정 파일의 위치를 가리키는 기능을 함
- 위치(path)만 가리키므로 다른 파티션, 모든 종류의 file에 만들 수 있음
- 가리키는 대상의 UNIX file mode를 따라가므로 symlink 권한은 777이며 의미가 없음
- symlink가 가리키는 대상의 주소가 relative path라면 symlink를 기준으로 만들어야 함(그러므로 symlink를 만들 디렉터리에서 작업하는 것이 좋음)
- 다른 위치에서 작업한다면 -r 옵션을 추가해서 relative path를 계산하도록 하기

- i-node 파일에 실제 data하고 연결된 것(숫자로 관리)

# 02. inode
- 파일의 메타 정보 및 관리용 객체
- 파일은 고유의 i-node 1개를 가지고 있음
- i-node는 disk partition(or volume)내에서 유일한 식별자
- i-num이라고도 부름
- partition이나 volume이 달라지면 의미가 없는 번호
- file meta info: 시간 관련 정보, 사이즈, 소유권, 권한 등등

# 03. which
- path에 존재하는 파일을 검색

# 04. readlink
### readlink
- Symlink가 여러 단계로 가리키는 파일이 있을 수 있음
- 그래서 symlink의 canonical path를 따라가는 기능이 있음
- readlink -f <symlink>: canonical path를 따라가면서 마지막 링크를 제오횐 모든 링크가 존재할 때 성공
- readlink -e <symlink>: canonical path를 따라가면서 모든 링크가 존재할 때 성공

### canonicalization
- canonical이란 컴퓨팅 환경에서 실체를 가지는 standard, official의 의미를 가짐
- A나 B가 정해져야만 official한 의미가 되는경우, 즉 상대적인 의미를 해석한 뒤 standard, official의 대상을 한정적으로 하는 행위

### Symlink를 사용하는 예
- 음악 추천
- library 버전 관리

### Practice: symlink
sym3 => sym2 => sym1 => hardlink 형태로 만들기

- touch 명령으로 빈 파일 하나 만들기
> &#36; touch hardlink
> 
> &#36; ln -s hardlink sym1
> 
> &#36; ln -s sym1 sym2
> 
> &#36; ln -s sym{2, 3}
- brace expansion: sym{2, 3}은 sym2, sym3이 됨, 반복되는 문자열이 있다면 유용하게 사용

- 확인하기
> &#36; ls -l sym* hardlink

- hardlink까지 바로 앞단계까지 보여주기
> &#36; readlink -f sym2

- hardlink 맨 끝까지 보여주기
> &#36; readlink -e sym2

- 하드링크 지우고 다시 앞단계까지 보여주기
> &#36; rm hardlink
> 
> &#36; readlink -f sym3

- 모든 링크가 다 살아있어야 하므로 명령 fail
> &#36; readlink- e sym3

### Practice: canonicalization
- locate 명령 확인해보기
> &#36; which locate

- ls -l: locate 확인해보기(심볼링크로 가르키고 있는 것 있음)
> &#36; ll /usr/bin/locate

- 가르키고 있는 심볼링크 ls -l로 확인해보기
> &#36; ll /etc/alternative/locate

- 가르키고 있는 심볼링크 ls -l로 확인해보기
> &#36; ll /usr/bin mlocate

- readlink로 한번에 따라가기
> &#36; readlink -e /usr/bin/locate