---
title: "Linux Command(2) - File 관련 명령어"
excerpt: ""

categories:
  - Linux
tags:
  - UNIX
  - UNIX Command
  - Linux
  - Linux Command
---
# 01. Path
- pwd: print working directory
- cd: change directory
  - &#47;: 루트 디렉토리
  - &#126;: 홈 디렉토리
  - &#8211;: 이전 경로
  - 경로 생략시 홈 디렉토리로

# 02. Directory: 절대/상대 경로
- absolute path(절대 경로): root directory(&#47;)를 시작으로 하는 경로
- relative path(상대 경로): 현재 디렉터리(&#46;)를 시작으로 하는 경로(.은 생략 가능)

# 03. ls
- ls &#8211; list file
- file type: [&#8211;dbclps]
  - &#8211;: 일반 파일
  - d: directory 파일
  - l: symbolick link
- list
  - a: all
  - l: long
  - t: sort by mtime
  - r: reverse
  
# 04. Unix file mode
- file mode bit: UNIX의 파일 권한을 나타내는 3+9 bit 체계
  - 숨겨진 3bit를 포함하여 12bit지만 화면에넌 9칸만 표시
- 3+9 bit의 UNIX file mode
  - 3bit: SetUID, SetGID, Sticky bit
  - 9bit: 파일의 owner, group, others
- 표기 방법
  - Symbolic mode: "rwx" symbol로 표기하는 방식
  - Octal mode: bit를 8진수법으로 표기하는 방식
  
# 05. Octal mode
- rwxrwxrwx: 3칸씩 각각의 rwx = owner, group, others 파트로 나뉨
- r(4): readable
- w(2): writeable
- x(1): executable
- directory인 경우
  - r(4): readable file list
  - w(2): writeable file list
  - x(1): accessible
- 파일 생성시 기본값
  - 기본 mode값은 umask값을 뺀 나머지(예를 들어 umask 022이면 644)
  
# 06. mkdir, rmdir
- mkdir: make directory
- rmdir: remove directory
  - 디렉토리가 비어있는 경우에만 삭제 가능
  - r: recursively
  - f: force
  - rmdir 대신아 rm -rf로 파일과 디렉터리를 함께 지우는 경우가 많음
  
- dir의 r 권한은 dir의 목록을 읽을 수 있는 기능
- dir의 x 권한은 dir의 access 할 수 있는 기능

# 07. cp, mv, rm
- cp: copy
- mv: move, rename
- rm: remove

# 08. chmod, chown, chgrp
- chmod: change mode
- chown, chgrp: change owner/group
- root 유저만 가능

# 09. file <file>
- 파일의 타입 확인
  - magic 데이터(/usr/share/file/magic)
  
# 10. stat [option] <file>
- file의 meta data 출력
- meta data: 내용이 아닌 수식하는 정보, 예를 들어 파일이름, 생성시간, 권한 등등
- Access: 최근에 읽은 시간
- Modify: data가 변경된 시간(mtime)
- Change: meta-data가 변경된 시간(ctime)

# 11. touch <file>
- 파일의 메타 정보 업데이트
- file이 존재하지 않는 경우 빈 파일의 생성

# 12. find
- find directory [expression]
- 검색 후 작업 지시 가능
### 조건의 표기
  - n: 정확히 n인 경우를 검색
  - +n:n보다 큰 경우 검색(n 포함)
  - -n:n보다 작은 경우 검색(n 포함)
  
### 주요 검색 조건
  - -name filename: filename의 이름과 같은 파일 검색
  - -size n: 크기가 n인 파일을 검색
  - -mtime n, -mmin n: 변경된 시간이 n인 파일 검색(mtime: day, mmin: minute)
  - -inum n: inode number가 n인 파일 검색
  - -samefile file: file과 같은 inode를 가진 파일 검색
  - maxdepth level: 탐색할 위치의 하위 디렉터리 최대 깊이가 level인 파일 검색
  
### invalid expression
  - find . -name *.txt: 현재 디렉터리에 *.txt에 매칭되는 파일이 있으면 쉘의 wildcard 해석 기능으로 인해 변경
  - find . -name "*.txt": quitation marks로 감싸주기
  
### 검색 후 작업 지시
  - find .... -exec 명령어 &#92;; → 매번 한 번씩 실행
  - find .... -exec 명령어 &#92;+ → 합쳐서 한 번만 실행

### Practice 4-a
현재 디렉터리 아래에서 최근 24시간 이내에 내용이 변경되어진 일반 파일을 찾아서 mtime_b24.txt파일로 리스트를 저장해보기
- 조건 1) mtime이 24시간 이내
- 조건 2) 일반 파일: -type f
> find ./ -mtime -1 -type f &#62; mtime_b24.txt

### Practice 4-b
연습문제 1에서 조건 추가
현재 디렉터리에서 3단계 아래를 넘어가는 경우를 검색하지 않으며, 조건에 만족하는 파일들은 모두 "~/backup" 디렉터리에 복사하기
- 조건 1) -maxdepth 3
- 조건 2) -exec
> find ./ -maxdepth 3 -mtime -1 -type f &#62; mtime_b24.txt -exec cp {} ~/backup \;