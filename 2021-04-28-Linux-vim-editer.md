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
# vim editor: Linux에서 가장 많이 쓰이는 텍스트 편집기
- vi(visual editor)
  - vim의 조상
  - UNIX/Linux에서 가장 많이 사용하는 에디터
  - BSD의 Bill Joy가 개발(1976)
  
- vim(vi improved)
  - 시간이 지날수록 vi의 기능이 구식이 되어가는 과정에서 vi 대체
  - UNIX 이전의 Amiga에서 먼저 사용되던 시스템
  - Linux에서 vi 대신에 vim이 사용됨
  - root 유저가 사용하는 vim은 최소화된 vim
  
- vim: platform
  - 다양한 플랫폼 지원
  - Linux, UNIX, Mac OSX, Windows
  
- vim의 설치
  - vi(제한된 기능의 vim)으로 설치되는 경우가 많음
  - RH계열: RHEL, CentOS, Fedora
  > # yum -y install vim-enhanced
  - Debian 계열: Debian, Ubuntu, Mint...
  > $ sudo apt -y install vim
  - 의존성에 의해서 실패하는 경우에는 의존성을 해결해야 함
  
- vim의 시작
  - vim [filename]
  - 특정 파일명을 열면서 시작
  > $ vim mytext.txt
  - 파일명이 "-"일 경우에는 -stdin- 을 의미함
  > $ find . -name "*.txt" vim -
  
- 모드
  - 전통적인 vi의 모드
    - 일반 모드(normal mode)
    - 입력 모드(insert mode)
    - 명령행 모드(command-line mode)
  - vim의 추가모드
    - 비주얼 모드(visual mode): 마우스를 대신하는 드래그 모드
  
- 모드 전환 사진 구글링해서 추가하기
  - Normal mode -> Insert mode: a, i, o(A, I, O, R)
    
    | 명령어 | 설명 |
    |:---|:---|
    |**a**|**a(append)**는 현재 커서 위에서 한 칸 뒤로 이동한 후 입력 모드로 전환|
    |A|A는 현재 행의 끝으로 이동한 후, 입력 모드로 전환|
    |i|i(insert)는 현재 커서 위에서 입력 모드로 전환|
    |I|I는 현재 행의 맨 앞으로 이동 후, 입력 모드로 전환|
    |**o**|**o(open line)**는 현재 행 아래에 새로운 행을 하나 만든 후 입력 모드로 전환|
    |O|O는 현재 행 위에 새로운 행을 하나 만든 후 입력 모드로 전환|
    |R|수정(replace) 모드로 작동하므로 모든 글자는 덧쓰여짐|
    
  - Insert mode -> Normal mode: ESC
  - Normal mode -> Command line mode: :(colon)
  - Command line mode -> Normal mode: ESC, 실행
  
- commandline: exit
  - 메모장에서 txt를 입력하고 x 버튼을 누르면 저장, 저장 안 함, 취소 창이 뜸
  - vi에서 저장을 하지 않고 나가면 error(E37)가 남
  - 저장하지 않고 나가기 위해서는 !를 추가함(force 명령)

- normal mode가 필요한 이유
  - GUI 화면에서는 menu가 있지만 text editor 방식에서는 GUI menu가 없으므로 단축키(short-cut)으로 구현
  - Ctrl과 Shift를 누르지 않고 영문자만 눌러도 작동하게끔 한 것이 normal mode
  
- cursor
  - normal mode에서 화살표키 대신에 h(←), j(↑), k(↓), l(→) 사용
  - 과거에는 키보드 모양이 없었기 때문
  
- cursor: movement 
  
  |명령어|설명|
  |:---|:---|
  |[#]h|좌로 #칸 이동, #의 생략 시는 1칸|
  |[#]j|아래로 #칸 이동, #의 생략 시는 1칸|
  |[#]k|위로 #칸 이동, #의 생략 시는 1칸|
  |[#]l|우로 #칸 이동, #의 생략 시는 1칸|
  |^|행의 맨 앞으로 이동|
  |$|행의 맨 끝으로 이동|

- scroll

  |명령어|설명|
  |:---|:---|
  |Ctrl-B(=PageUP)|위로 한 화면 스크롤|
  |Ctrl-F(=PageDown)|아래로 한 화면 스크롤|
  |Ctrl-U|위로 1/2화면 스크롤|
  |Ctrl-D|아래로 1/2화면 스크롤|

- goto #line
  - 특정 라인으로 이동할 떼 scroll을 사용하는 것은 비효율
  
  |명령어|설명|
  |:---|:---|
  |[#]gg|#행으로 이동, #이 생략되면 1 의미|
  |[#]G|#행으로 이동, #이 생략되면 마지막 행 의미|
  |:#|# 행으로 이동|
  |Ctrl-G: file|현재 문서 위치 정보를 하단 상태 바에 표시|

# vim 일반 모드 기초 명령어
- 삭제
  - vi에서의 삭제: 임시 버퍼에 잘라내기 
    
  |명령어|설명|
  |:---|:---|
  |x|커서에 위치한 문자 삭제(<delete>키와 같음)|
  |dd, :d|현재 행을 삭제|
  |D|현재 칼럼 위치에서 현재 행의 끝부분을 삭제(d$와 동일)|
  |**J**|아래 행을 현재 행의 끝에 붙임(아래 행의 앞부분 공백은 제거됨)|

- 붙여넣기(Paste)
  
  |명령어|설명|
  |:---|:---|
  |p|현재 행에 붙여넣기(put)|
  |:pu|개행 문자가 포함된 경우에는 현재 행의 아래에 붙여넣기|
  |P|현재 행의 위쪽에 붙이기|

- 복사하기(Copy)

  |명령어|설명|
  |:---|:---|
  |yy, :y, Y|현재 행을 레지스터에 복사(yank)|

- undo/ redo

  |명령어|설명|
  |:---|:---|
  |u|undo 기능, 바로 이전에 행한 명령 한 개 취소|
  |Ctrl-R|redo 기능, 바로 이전에 취소했던 명령 다시 실행|
  |.(dot)|이전 명령어 반복|

- cmd: range
- 명령행 모드에서 범위를 지정해서 명령

  |명령어|설명|
  |:---|:---|
  |:20d|20번 행 삭제|
  |:10,25d|10~25번 행 삭제|
  |:10,$d|10~마지막 행까지 삭제|
  |:%y|문서 전체를 복사(%는 1, $)와 동일|
  |:.,+20y|현재 행부터 아래로 스무 행 복사|
  |:-10,+5d|현재 행부터 위로 10행, 아래로 5행, 총 열여섯 행 삭제|
  |:40pu|40번 행에 레지스터의 내용을 붙여넣기|

- 범위 연산 meta character

  |명령어|설명|
  |:---|:---|
  |.|현재 행|
  |$|마지막 행|
  |+#|현재 위치에서 #만큼 아래 행|
  |-#|현재 위치에서 #만큼 위 행|
  |%|문서(파일) 전체|
  
- Visual mode
  - mouse 대신 drag 기능
  - 한글에서 F3키와 같은 기능
  - visual mode에서 colon을 누르면 자동으로 range 설정

  |명령어|설명|
  |:---|:---|
  |v|일반 비주얼 모드로 현재 커서 위치에서 블록 지정|
  |V|visual line mode로 현재 커서가 위치한 행에서 행 단위로 블록 지정|
  |Ctrl-V|visaul block mode로 열 단위로 블록 지정(Ctrl-Q로 대체 가능)|

- visual block: column editing
  - Ctrl-V, 에디팅(I(isnert), A(append), R, C(change)...), ESE*2
  - 특정 열에 문자열을 삽입하거나 교체하는 경우
  
- visual mode

  |명령어|설명|
  |:---|:---|
  |gv|previous highlighted text 영역 불러오기|
  |o|highlight text 블록의 시작, 끝 이동|

# vim 클립보드 & options(.vimrc 설정 파일)
- vim의 바이너리 종류
- vi
  - vi 호환의 vim 
  - vim 종류 중에 가장 적은 기능
  - compact vim, tiny vim
  - 요즘은 거의 사용하지 않음
  
- vim
  - 일반적인 vim
  - vi에 추가적인 기능과 플러그인 사용이 가능
  - enhanced vim
  
- vimx
  - 가장 많은 기능을 가진 vim
  - vim + clipboard, X window clipboard 기능이 포함
  
- package name
  
  |기능|RedHat 계열 패키지|Debian 계열 패키지|
  |:---|:---|:---|
  |일반 vi|vim-minimal(명령어: vi)|vim-tiny(명령어: vi)|
  |enhanced vim|**vim-enhanced**(명령어: vim)|**vim-nox**(명령어: vim)|
  |vim with X|vim-X11(명령어: vimx, gvim)|vim-gnome / vim-athena(명령어: vim, gvim, evim)|

- 패키지 설치
  - RedHat 계열
  > yum -y install vim-X11  (설치)
  > yum -y remove vim-X11 (삭제)
  - Debian 계열
  > sudo apt -y install vim-gnome (설치)
  > sudo apt -y --auto-remove remove vim-gnome (삭제)

- GNOME(21세기) vs Athena(20세기)

- alias: vi, vim(vi의 에일리어스)
  - 일반 유저의 경우 alias vi=vim으로 잡혀 있음
  - alias를 무시하고 싶을 때는 앞에 \를 붙여서 \vi로 실행
  
- vimx
  - vimx는 클립보드 공유 기능이 있음
  - 클립보드 공유란? 
  - 나중에 나온 x window와 termenal은 호환이 되지 않음
  - clipboard, xterm_clipboard 기능이 포함되어 있는지 확인
  > $ vim --version | grep clipboard
  
  - vimx를 사용할 때 vim-gnome을 설치했더라도 설정을 해줘야 함
  - ~/.vimrc 환경설정
  > if has('unnamedplus')
  >   set clipboard=unnamed,unnamedplus
  > endif
  
- 명령행모드: 옵션 설정
  - :set name[=value]
  - .vimrc 지정할 때는 :은 필요없음
  |명령어|모음|
  |:---|:---|
  |:set|현재 옵션 설정을 보여줌|
  |:set all|모든 옵션 설정을 보여줌(default 옵션까지 출력)|
  |:set [no]name|name에 해당하는 옵션을 on/off 함|
  |:set name!|name 옵션의 on, off를 toggle 함|
  |:set name=value|name 옵션에 value의 값을 할당함|

- 주로 사용하는 옵션

  |옵션|설명|
  |:---|:---|
  |nu|(number)화면에 행 번호 표시|
  |rnu|(relative nu)현재 행을 기준으로 상하의 상대적 행번호 표시|
  |ai|(auto indent)자동 들여쓰기|
  |cindent|C언어 스타일의 들여쓰기|
  |ts=value|(tab stop)화면에 표시될 탭 크기 value로 지정|
  |sw=value|(shift width)자동 블록 이동시 열의 너비|
  |fencs=value|(file encodings)지원할 파일 인코딩 방식 리스트|
  |fenc=value|(file endcoding)현재 파일 인코딩 방식을 지정|

- .vimrc 예시
> set ai cindent
> set ts=4 sw=4
> set nu
> set fencs=ucs-bom,utf-8,korea

- colorscheme
> :colorscheme "여기서 <Tab> 키를 누를때마다 한개씩 사용가능한 테마가 나옴 or <Ctrl-D>로 탐색
  - 요즘 vim custom colorscheme는 github에서 많이 사용(molokai, jellybeans, material)

# vim의 매뉴얼
- vim은 online help를 지원함
> :help word "<Ctrl-D>

- 도움말에 사용되는 접두어
  
  |모드|접두어|예|
  |:---|:---|:---|
  |일반|없음|:help x|
  |입력|i_|:help i_Ctrl-N|
  |명령행|:|:help :w|
  |비주얼|v_|:help v_u|
  |vim 실행인수|-|:help -r|
  |옵션|'|:help 'tabstop'|
  |명령행 모드 특수키|c_|:help c_Ctrl-B

- help
  - Ctrl-]: 커서 아래 있는 키워드의 정의로 이동
  - Ctrl-T: 이전 tag로 이동(되돌아오기)
  - Ctrl-W-W: window 사이를 이동
  
# vim의 error
- 파일을 중복해서 오픈한 경우
  1. 다른 프로그램이 동일한 파일을 편집하고 있는 경우 
    - 원인: 위의 process ID 옆에 still running(실행 중)일 때 
    - 해결방안: O(open Read-Only), Q(quit)
  2. edit session이 crash를 받아서 죽은 경우
    - 원인: 위의 process ID 옆에 still running(실행 중)이 없을 때
    - 해결방안: R(recover)
  
# 문자열 관련 기능
- Formatting: center, right, left
  - width를 기준을 정렬

- find(문자 하나 검색)
  
  |명령어|설명|
  |:---|:---|
  |fc|문자 c를 전방 검색|
  |Fc|문자 c를 후방 검색|
  |;|최근 검색을 재검색|
  |,|최근 검색을 반대 방향으로 재검색|

- find(문자 여러개 검색)
  - /string: string 전방탐색
  - ?string: string 후방탐색
  - *: 현재 커서에 위치한 단어를 전방탐색
  - #: 현재 커서에서 위치한 단어를 후방탐색
  - n: 다음 탐색 결과
  - N: n과 반대방향으로 다음 탐색
  - %: 괄호의 짝을 찾아줌
  
- case-sensitive searching
  - \c: 대문자나 소문자를 상관하지 않고 검색
  - :set ignorecase: defalut 기능으로 대소문자를 가리지 않게 함
  
- :nohl: 일회성 highlightsearch 해제
- :set nohl: highlightsearch 옵션 해제

# 문자열 관련 기능
- 교체: sed의 기능이 import된 것(sed의 문법과 동일)
- :[range]s/<찾는 문자열>/<교체할 문자열>/<option>(/ 대신에 다른문자(ex. ,) 가능)
- option
  - g: (global) 검색된 문자열 모두를 교체
  - i: (ignore case) 대소문자 무시
  - c: (confirm) 교체할 때마다 Yes/No 확인
  - e: 교체 과정 중 에러 무시
  
- 특수 문자(예: New Line)의 교체
  
  |형식|개행 문자|
  |:---|:---|
  |DOS/Windows|CR+LF|
  |UNIX|LF|
- CR의 입력: ^V^M
- 실전에서는 :set ff=dos 혹은 :set ff=unix로 설정 후 저장하는 방법 사용

- ASCII

  |입력 방법|설명|
  |:---|:---|
  |<Ctrl-v> ###|###에 10진수를 사용하여 ASCII 입력|
  |<Ctrl-v> o###|###에 8진수를 사용하여 ASCII 입력|
  |<Ctrl-v> x##|##에 16진수를 사용하여 ASCII 입력|
  
# file 관련 기능
- vim terminology
  - buffer: 파일을 편집하기 위한 임시 공간
  - register: 텍스트 일부를 저장하고 있는 임시 공간
  
- vim에서 다른 파일을 편집하려고 할 때

  |명령어|설명|
  |:---|:---|
  |:e[filename]|filename을 편집모드로 오픈(filename이 생략되면 현재 파일 다시 오픈)|
  |:e#[count], CTRL-^|count번째 파일 오픈(count가 생략되면 바로 이전 파일)|
  |:find filename|filename에 해당하는 파일을 검색하여 오픈(매칭 결과가 복수 개이면 에러 메시지 출력)|

- multiple buffers:
  > vim file1 file2 file2 ...
  :n으로 이동
  
- 종료 관련 기능
  
  |명령어|설명|
  |:---|:---|
  |:q[!]|현재 창 종료(!는 강제 종료)|
  |:qa[i]|모든 창 종료(복수개의 창을 열고 있을 때 유용)|
  |:wq|저장하면서 종료(:x를 더많이 사용)|
  |:wqa|모든 창을 저장하면서 종료|

- 파일 저장
  
  |명령어|설명|
  |:---|:---|
  |:w [filename]|filename이 지정되면 해당 파일에 쓰기 지정, filename이 생략되면 현재 파일에 쓰기 지정|
  |:sav file|현재 파일을 다른 이름으로 저장(편집중인 파일도 새로 저장된 파일로 교체)|
  |:up|변경된 점이 있는 경우만 ":w" 명령어 실행|
  |:x|up + quit(일반 모드에서는 ZZ)|

- 파일 저장의 예

  |명령어|설명|
  |:---|:---|
  |:10, 50w history|10~50번 행을 history라는 파일로 저장|
  |:.,+10w history|현재 행에서 아래로 10번행을 history라는 파일로 저장|
  |:10,$w history|10~끝까지 history라는 파일로 저장|
  |:%w history|전체를 history 파일로 저장|

- netrw: vim으로 디렉터리 열기(browsing)
- :e <디렉토리>: vim으로 디렉토리 열기
- F1: <help>

  |명령어|설명|
  |:---|:---|
  |<Enter>|파일을 현재 창에 열어줌|
  |**i**|파일 표시 방법 변경|
  |**s**|정렬 방식 바꿔줌|
  |**o**|커서 위치의 파일을 수평 분할된 새 창으로 열어줌|
  |**v**|커서 위지를 파일을 수직 분할된 새 창으로 열어줌|
  |P|커서 위치의 파일을 미리보기 창으로 열어줌|
  |t|새로운 탭으로 분할하여 열어줌|
  |-|상위 디렉터리로 이동|

# 복수의 파일 다루기
- split: horizontal
  > <Ctrl+W> s
  > :sp [file]
  
- split: vertical
  > <Ctrl+W> v
  > :vs [file]
  
- split: cmd
- 창 분할, 생성 명령

  |명령어|설명|
  |:---|:---|
  |:[#]sp [파일명] / [#]Ctrl-W s|상하로 창을 분할, 파일명을 생략하면 현재 파일 의미(#는 분할창의 크기)|
  |:[#]vs [파일명] / [#]Ctrl-W v|좌우로 창 분할|
  |:[#]new / [#]Ctrl-W n|상하로 분할하고, 위쪽에 새로운 창 만들기|
  |:[#]vnew / [#]Ctrl-W v|좌우로 창을 분할하고, 왼쪽에 새로운 창 만들기|

- 창 이동 관련 명령

  |명령어|설명|
  |:---|:---|
  |Ctrl-W Ctrl-방향키 / Ctrl-방향키|h, j, k, l이나 화살표 키로 이동|
  |Ctrl-W Ctrl-W / **Ctrl-W w**|현재 창에서 오른쪽 방향으로 이동|
  |Ctrl-W Ctrl-P / Ctrl-W p|바로 이전에 사용한 창 이동|

- 창 크기 관련 명령

  |명령어|설명|
  |:---|:---|
  |Ctrl-W=|모든 창의 크기를 동일하게 조절|
  |Ctrl-W [#]+|# 크기만큼 크기를 키움(# 생략하면 1)|
  |Ctrl-W [#]-|# 크기만큼 크기를 줄임(# 생략하면 1)|

- split: diff
  - **vim -d file1 file2**: 소스 코드를 비교할 때나, 설정 파일 비교할 때 많이 사용
  
- tab page vs split-window
  - 분할 할때마다 원래 창의 크기가 줄어들기 때문에 텝 페이지 기능이 필요
  
- 탭 열기 vim 실행 옵션: -p
  > vim -p file1 file2 file3...
  
  |명령어|설명|
  |:---|:---|
  |:[#]tabe[dit] file|#번째 탭에 파일 열기(# 생략되면 현재 탭 뒤에 생성)|
  |:[#]tabnew file|#번째 위치에 비어있는 탭 만들기|
  |:[#]tabc[lose]|#번째 탭을 닫기(그냥 q로 닫아줘도 됨)|

- 탭 사이 이동 명령(tabnext, tabprev)
  - [#]tabn, [#]tabp
  - [#]gt와 [#]gT를 더 많이 사용
  
# buffer
- vim에서 왜 buffer라고 부르는지: 아직 저장 전이면 이름이 없는 공간이므로
- :files 혹은 :buffers로 현재 버퍼 목록을 볼 수 있음
  
- 파일 번호에 상태 플래그

  |명령어|설명|
  |:---|:---|
  |%|현재 편집 중인 버퍼|
  |#|바로 이전에 열었던 버퍼 혹은 다음에 열도록 예비된 버퍼|
  |a|활성된 버퍼ㅕ현재 화면에 보이는 버퍼)|
  |+|변경된 부분이 있는 버퍼|

- 파일 이름 아래에 경로명을 인식해서 파일을 오픈하는 기능
- gf(이동), Ctrl-^(이전파일로 되돌리기)

- 커서 위 파일 열기 명령어

  |명령어|설명|
  |:---|:---|
  |gf|커서 위치의 파일명을 인식해서 열어주기|
  |<Ctrl-W>f|커서 위치의 파일명을 분할된 창에 열어주기|
  |**<Ctrl-W>gf**|커서 위치의 파일명을 탭에 열어주기|
  |<Ctrl-^>|이전 파일로 되돌아가기|

# 파일 인코딩
- fileencodings (fencs)
  - 파일을 읽을 때 확인할 encoding list를 설정
    - 설정된 list를 순서대로 확인하면서 변환이 필요한 경우를 지원함
    - .vimrc에 설정해야만 multi-bytes 기반 인코딩(CJK) 파일을 읽을 수 있음
  - set fencs=value
    - fence=ucs-bom,utf-8,korea-latin-1
    - BOM 확인(무조건 써주기) 후, UTF-8, korea 체크(euc-kr,cp949), 마지막엔 ASCII 순서로 읽기
  - BOM(Byte Order Mark)
    - USC(Universal coded Character Set)의 판별 마크: 생략하거나 맨 앞에 사용해주기
  
- fenc, fencs에 사용 가능한 문자 세트(:help로 볼 수 있음)

  |명령어|설명|
  |:---|:---|
  |**utf-8, utf8**|UTF=8 유니코드 형식|
  |ucs-bom|BOM 마크에 의한 유니코드 형식|
  |**korea**|한글 지원 별칭(유닉스에는 euc-kr, 윈도에서는 cp949로 자동 변환)|
  |euc-kr|한글 지원(유닉스에서만 사용 가능)|
  |cp949|한글 지원(유닉스, 윈도우 모두 사용 가능)|
  |japan|일본어 지원 별칭(유닉스: euc-jp / 윈도우 cp932로 자동 변환)|
  |latin1, ansi|영문 ASCII 형식|

- Linux에서 만든 텍스트 파일을 Window로 가져갈 때 UTF-8을 지원하는 에디터가 아니면 변환을 거쳐야 함
  - :set ff=dos(NewLine 변경)
  - :set fenc=korea(Hangul 변경)
  - :wq(다른 이름으로 저장 시 :sav)
  
- Window에서 Linux로 파일을 가져올 때, fencs가 제대로 설정되어 있다면 실행 시 자동 변경
  - :set ff=unix
  - :set fenc=utf8
  - :wq(다른 이름으로 저장 시 :sav)
  
# 편리한 기능
- 단어 경계 이동: w(단어의 앞에서 멈춤), e(단어의 뒤에서 멈춤)

  |명령어|설명|
  |:---|:---|
  |0|0번째 열|
  |^|공백이 아닌 실제 내용이 있는 시작 열|
  |$|마지막 열(행의 끝)|
  |w|단어의 시작 위치 혹은 문장 부호의 경계를 따라서 이동(words forward)|
  |e|w와 같으나, 단어의 끝 부분에 위치(end of word)|
  |b|w와 비슷하나 진행 방향이 역방향(words backward)|
  |W, E, B||

- 괄호, 문단, 블록 단위 이동

  |명령어|설명|
  |:---|:---|
  |%|가장 가까운 괄호 짝으로 이동|
  |(,)|문장 단위의 시작 위치, 끝 위치로 이동|
  |{,}|문단 단위의 시작 위치, 끝 위치로 이동|
  |[[,]]|블록 단위의 시작 위치, 끝 위치로 이동|
 
- 특정 단어 입력 시 대체 입력하는 기능: abbreviation
  - ab, ia 기능 이용
  - ab: abbreviation
  - ia: insert mode에서만 작동하는 기능
  - ca: commandline mode에서만 작동하는 기능
  
- ca 기능을 이용하면 한글 상태 오타를 변환할 수 있음
  > ca ㅈ w
  > ca ㅈㅂ wq
  > ca ㅂ q!
  > ca ㅌ x
- .vimrc 파일의 인코딩 형식에 주의(.vimrc 파일이 EUC-KR로 되어 있으면 UTF-8 문서 편집시 작동하지 않을 수 있음)

- abbreviation: cmds

  |명령어|설명|
  |:---|:---|
  |:ab [lhs]|현재 설정된 모든 약어 목록 출력(lhs에 약어를 지정하면 해당 약어의 정보만 출력)|
  |:ab {lhs} {rhs}|약어 lhs를 rhs로 변환함|
  |:unab {lhs}|약어 lhs를 해제|
  |:abclear|설정된 모든 약어를 해제|
  |:ia {lhs} {rhs}|ab와 기능은 같지만 입력 모드에서만 작동|
  |:ca {lhs} {rhs}|ab와 기능은 같지만 명령행 모드에서만 작동|

- 단축키 map
  - nmap <단축키> <명령>: normal mode 에서만 작동
  - imap <단축키> <명령>: insert mode 에서만 작동
  - map <단축키> <명령>
  
- key map

  |명령어|설명|
  |:---|:---|
  |nmap key command|일반 모드에서 key를 누르면 command 실행|
  |imap key command|입력 모드에서 key를 누르면 command 실행|
  |vmap key command|비주얼 모드에서 key를 누르면 command 실행|
  |cmap key command|명령행 모드에서 key를 누르면 command 실행|

- autocmd: 특정 상황에서 자동으로 실행할 명령어
> autocmd BufRead,BufNewFile *.txt colo evning
버퍼를 읽거나 새로운 파일을 만들 때 파일 명이 .txt이면 colo를 색상을 evning으로 변경
- 특정 경로명 패턴 인식 가능

# For Programmer
- 들여쓰기 다시하기
  - ={motion}: 키(이동 관련)를 먼저 입력하고 모션을 입력(전체 retab: gg → = → G)
  - visualmode + =: (비주얼모드로 라인 선택 후(v, V) =)
  
- 기존에 작성된 탭 문자를 공백 4칸으로 전환하고 싶으면
> :set et ts=4
> :ret

- 반대로 공백 4칸을 탭 문자로 변환하려면?
> :set noet ts=4
> :ret!

- 자동 완성 기능 <Ctrl-N>
- vim은 #include 구문 안에 까지 
- <Ctrl-N>: 단어 완성을 위해서 현재 문서와 관련 파일을 전방 탐색
- <Ctrl-P>: <Ctrl-N>과 반대 방향으로 탐색

- 자동 완성 기능(특수 문자가 포함된 경우)
- <Ctrl-N> <Ctrl-X> </Ctrl-N>

- 사전 기반 검색, 파일명 검색
  - <Ctrl-X><Ctrl-N>: 더하기 낱말 모드로 작동하여 추가 검색(원하는 낱말이 아닐 경우 <Ctrl-N> 계속 누르기)
  - <Ctrl-X><Ctrl-K><Ctrl-N>: 사전 검색 모드로 작동
  - <Ctrl-X><Ctrl-F>: 파일명 검색 가능
  
- vim-bootstrap: 자동으로 vimrc를 만들어주는 곳(https://vim-bootstrap.com)
- 여기서 만들어진 vimrc를 약간 수정해서 사용하느 사람들이 많음
- 