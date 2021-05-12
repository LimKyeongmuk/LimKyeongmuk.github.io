---
title: "Linux Command(4) - Archive / Compress"
excerpt: ""

categories:
  - Linux
tags:
  - UNIX
  - UNIX Command
  - Linux
  - Linux Command
---
# 1. archive(보관용 묶음), compress(압축)
- UNIX 계열은 여러 파일을 묶는 작업과 압축이 분리되어 있음
- 아카이브 유틸: tar, cpio
- 압축 유틸: gzip, bzip2, xz, zstd, lz4

# 2. 아카리빙: tar
- tar[ctxv] [f archive-file] files...
- c(create): 생성
- t(test): 테스트
- x(extract): 아카이브로부터 파일 풀어냄
- v(verbose): 상세 정보 출력 -> 실무에서 사용 안함
- f archive-file: 입출력할 아카이브 파일 명
- --exclude file: 대상 중에 file을 제외(특정 파일을 제외할 때 사용)

# 3. Compress
### 압축: 프로그램의 발전
- compress(z) -> gzip(gz) -> bzip2(bz2) -> **xz(xz)** -> lz4(lz4) -> **zstd(zst)**

### 압축: gzip
- gzip [-cdflrv] <file ...>
  - d: (decompress) 압축해제
  - c: (stdout) 표준 출력으로 결과물 보냄
  - 1, 9: (fast, better) 압축 레벨 지정
  
- tar과 gzip을 함께 사용하는 고전적 방법
  - 압축: tar c /etc/*.conf | gzip -c > etc.tar.gz
  - 해제: gzip -cd etc.tar.gx | tar x
  
### 압축: bzip2, xy
- gzip과 옵션 동일, 사용법 동일

### 압축: zstd
- 위와 동일

### verbose 옵션의 문제
- verbose는 화면 출력으로 인해 I/O delay가 증가

### tar extensions
- 편리한 GNU tar의 주요 옵션
  - z: 아카이브의 결과물을 gzip으로 압축 혹은 해제
  - j: 아카이브의 결과물을 bzip2으로 압축 혹은 해제
  - J: 아카이브의 결과물을 xy으로 압축 혹은 해제
  - --zstd: 아카이브의 결과물을 zstd로 압축 혹은 해제
  - a: auto, 확장자로 자동 판단(멀티스레드를 사용할 수 없음)
  
# 4. 아카이브 생성 및 압축
- 고전 
>  $ tar c ./data ./exp | gzip -c > bak_data.tar.gz
- 르네상스
>  $ tar cfx bak_data.tar.gz ./data ./exp
>  $ tar cfj bak_data.tar.bx2 ./data ./exp
>  $ tar cfJ bak_data.tar.xz ./data ./exp
- 모던 명령어
>  $ tar cfa bak_data.tar.zst ./data ./exp
>  $ tar cfa bak_data.tar.xz ./data ./exp
- 멀티 스레드
>  $ tar c ./data ./exp | xz -T0 > bak_data.tar.xz
>  $ tar c ./data ./exp | zstd -T0 > bak_data.tar.zst

# 5. 압축 풀기
- 고전 
>  $ gzip -dc bak_data.tar.gz | tar x
- 르네상스
>  $ tar xfz bak_data.tar.gz
>  $ tar xfj bak_data.tar.bx2
>  $ tar xfj bak_data.tar.xz
- 모던 명령어
>  $ tar xfa bak_data.tar.zst
>  $ tar xfa bak_data.tar.xz
- 멀티 스레드
>  $ xz -dcT0 bak_data.tar.xz | tar x
>  $ zstd -dcT0 bak_data.tar.zst | tar x