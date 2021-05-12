---
title: "ROS 프로그래밍 기초(1)"
excerpt: ""

categories:
  - ROS
tags:
  - ROS
  - ROS Programming
---
# ROS Package 기초
### ROS 패키지
- ROS에서 개발되는 소프트웨어를 논리적 묶음으로 만든 것

### ROS가 제공하는 편리한 명령들
- $ rospack list: 어떤 패키지들이 있는지 나열
- $ rospack find [package_name]: 이름을 이용해서 패키지 검색
- $ roscd [location_name[/subdir]]: ROS 패키지 디렉토리로 이동
- $ rosls [location_name[/subdir]]: Linux ls와 유사(경로를 몰라도 이름 적용 가능)
- $ rosed [file_name]: (환경 설정에 따른) 에디터로 파일 편집

# ROS 패키지 만들기
### 패키지를 담을 디렉토리로 이동
- $ cd ~/xycar_ws/src

### 패키지 새로 만들기
- $ catkin_create_pkg [패키지이름] [이 패키지가 의존하고 있는 다른 패키지들을 나열]

### 새로 만든 패키지를 빌드
- $ cd ~/xycar_ws
- $ catkin_make
- ~/.bashrc 파일에서 cm으로 alias 지정 해놓음
  
### 만들어진 패키지 확인
- $ rospack find my_pkg1
- $ rospack depends1 my_pkg1
- $ roscd my_pkg1

### 코드 작성
- ~/xycar_ws/src/my_pkg1/src 위치에 pub.py라는 이름으로 작성
  - rospy.Rate(1): 1초에 1번

### 프로그램 실행 권한
- 실행권한 부여
  - $ chmod +x pub.py
  
- ls -l 명령으로 실행권한 여부를 확인할 수 있음

# ROS 프로그램의 실행과 검증 확인
### 프로그램 실행과 확인
- 터미널 1: roscore 실행
- 터미널 2: turtlesim 노드 실행
- 터미널 3: pub.py 실행
- 터미널 4: rqt_graph 실행

1) $ roscore(터미널 1)
2) $ rosrun turtlesim turtlesim_node(터미널 2)
3) $ chmod +x pub.py(터미널 3)
4) $ rosrun my_pkg1 pub.py(터미널 3)
5) $ rqt_graph(터미널 4)
6) $ rosnode list(터미널 4)
- anonymous = true: 시리얼 번호를 붙여줌(시스템이 이름을 고유하기 위해서 붙여주는 이름)

### 구독자(Subscriber)를 살펴보기
- turtle이 어떤 토픽에 어떤 메시지를 발행하고 있는지 알아보기
  - $ rostopic list
  - $ rostopic type /turtle1/pose
  - $ rosmsg show turtlesim/Pose

- 토픽에 어떤 메시지를 발행하고 있는지 알아보기
  - $ rostopic echo /turtle1/pose
  
- ~/xycar_ws/src/my_pkg1/src 위치에 sub.py 코드 작성
  - rospy.spin(): 무한루프

### 프로그램 실행과 확인
- 터미널 1: roscore 실행
- 터미널 2: turtlesim 노드 실행
- 터미널 3: pub.py 실행
- 터미널 4: sub.py 실행

- 앞선 turtlesim_node와 pub.py가 실행되고 있는 상태에서
- $ chmod +x sub.py
- $ rosrun my_pkg1 sub.py

- 노드 동작 확인
  - $ rqt_graph
  
### Summary
- $ cd ~/xycar_ws/src
- $ catkin_create_pkg my_pkg1 std_msgs rospy
- $ cm
- $ cd ~/xycar_ws/src/my_pkg1/src
- $ gedit sub.py
- $ gedit pub.py
- $ chmod +x sub.py pub.py
- $ rosrun my_pkg1 pub.py
- $ rosrun my_pkg1 sub.py

# Launch 파일 기초
### roslaunch
- $ roslaunch [패키지 이름] [실행시킬 launch 파일 이름]
- *.launch 파일 내용에 따라 여러 노드들을 한꺼번에 실행시킬 수 있음
- 이때 [실행시킬 launch 파일]은 반드시 패키지에 포함된 launch 파일이어야 함.

### *.launch 파일
- roslaunch 명령어를 이용하여 많은 노드를 동시에 실행시키기 위한 파일
- 실행시킬 노드들의 정보가 XML 형식으로 기록되어 있음

### Tag
- node 태그
  - 실행할 노드 정보를 입력할 때 사용되는 태그
  - <node pkg="패키지명" type="노드가 포함된 소스파일명" name="노드 이름" />
  - 속성
    - pkg: 실행할 노드의 패키지 이름을 입력하는 속성(반드시 빌드된 패키지의 이름을 입력해야 함)
    - type: 노드의 소스코드가 담긴 파이썬 파일의 이름을 입력하는 속성(이때 파이썬 .py 파일은 반드시 실행 권한이 있어야 함)
    - name: 노드의 이름을 입력하는 속성(소스코드에서 지정한 노드의 이름을 무시하고, launch 파일에 기록된 노드의 이름으로 노드가 실행됨)
  
- include 태그
  - 다른 launch 파일을 불러오고 싶을 때 사용하는 태그
  - <include file="같이 실행할 *.launch 파일 경로" />
  - 속성
    - file: 함께 살행시킬 *.launch 파일의 경로를 입력하는 속성
  
### .launch 파일의 위치
- xycar_ws > src > Package > launch > *.launch
- package를 만들면 src는 자동으로 만들어주지만 launch 폴더는 자동으로 만들어주지 않음

### Launch 파일 생성
- 패키지 않에 디렉토리 아래 'launch'라는 디렉토리 만들고 그 안에 .launch 확장자의 파일을 만들어야 함
- $ gedit pub-sub.launch

### Launch 파일 실행
- $ roslaunch my_pkg1 pub-sub.launch
- $ roslaunch 명령을 사용할 때는 별도로 $ roscore 명령을 실행할 필요가 없음

### Summary
- $ cd ~/xycar_ws/src/my_pkg1
- $ mkdir launch
- $ cd ~/xycar_ws/src/my_pkg1/launch
- $ gedit pub-sub.launch
- $ cm
- $ roslaunch my_pkg1 pub-sub.launch

# Launch 파일의 tag 활용
### *.launch 파일 사례
- USB 카메라 구동과 파라미터 세팅을 위한 launch 파일

### *.launch에서 사용하는 Tag
- param 태그
  - ROS 파라미터 서버에 변수를 등록하고 그 변수에 값을 설정하기 위한 태그
  - <param name="변수의 이름" type="변수의 타입" value="변수 값" />
  - 속성
    - name: 등록할 변수의 이름
    - type: 등록할 변수의 타입, 사용할 수 있는 타입의 종류 str, int, double, bool, yaml
    - value: 등록할 변수의 값
  
  - ROS 파라미터 서버에 등록된 변수는 노드 코드에서 불러와 사용할 수 있음(rospy.get_param('~age'))
  - private parameter는 앞에 '~' 붙임
  
### Launch 파일에서 파라미터 전달 실습
- Launch 파일을 새로 만들어서 파라미터 값을 .launch 파일에서 읽어서 그에 맞게 동작하게끔 만들어보기
- 새로운 pub-sub-param.launch 만들기
  - $ gedit pub-sub-param.launch

- pub.py 파일을 복사, 수정해서 pub_param.py  파일을 새로 만들기
  - $ cp pub.py pub_param.py
  - $ gedit pub_param.py
  
- pub-sub-param.launch 파일에서 values 값을 수정하면 거북이의 회전 반경이 바뀜
  - $ roslaunch my_pkg1 pub-sub-param.launch