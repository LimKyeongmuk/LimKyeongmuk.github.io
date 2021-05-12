---
title: "ROS 노드 통신 프로그래밍(1)"
excerpt: ""

categories:
  - ROS
tags:
  - ROS
  - ROS Node Programming
---
# 노드 통신을 위한 패키지 만들기
- 전체 구성
  - teacher node(발행 노드)
  - student node(수신 노드)
  - 토픽: my_topic

- 패키지 생성
  - 디렉토리 구조
  
- 파이썬 프로그램 코딩

- Launch 파일 만들고 실행하기
  - Launch 파일 작성
  - cm(빌드)
  - 파이썬 파일에 실행 권한 부여
  - Launch 파일 실행

### Summary
- $ cd ~/xycar_ws/src
- $ catkin_create_pkg msg_send std_msgs rospy
- $ mkdir launch
- $ cd ~/xycar_ws/src/msg_send/src
- $ gedit student.py
- $ gedit teacher.py
- $ chmod +x student.py teacher.py
- $ cd ~/xycar_ws/src/msg_send/launch
- $ cm
- $ roslaunch msg_send m_sendlaunch


# 1:N, N:1, N:N 통신
- 토픽 메시지 타입 변경
  - String 대신에 Int32 사용

- 노드를 여러 개 띄울 때
  - 하나의 코드로 여러 개의 노드를 연결하려면 각 노드의 이름을 달리해야 노드의 init 함수에서 anonymous=True 값을 넣어주면 노드 이름이 자동으로 설정됨
  - rospy.init_node('student',anonymous=True)
  - anonymous=True: system이 실행시킬 때 이름 뒤에 숫자를 붙여주는 역할

- 노드를 여러 개 띄울 때
  - Launch 파일을 이용해서 roslaunch 명령으로 여러 노드를 띄울 수 있음
  - 노드 설정에서 name="ooo" 부분을 다르게 설정
  
- 1:N 통신
  - Launch 파일 바꾸기(m_send_1n.launch)

- N:1 통신
  - Launch 파일 바꾸기(m_send_n1.launch)
  
# 나만의 메시지 만들기
- Custom Message 사용 방법
  - $ cd ~/xycar_ws/src
  - $ roscd msg_send
  - $ mkdir msg
  - $ cd msg
  - $ gedit my_msg.msg
  - 새로운 custom msg 만들기
  
- Custom Messsage 선언
  - pacakge.xml 수정
  - /xycar_ws/src/msg_send 아래에 있음
  - 파일 아래 쪽에 내용 추가
    - <build_depend>message_generation</build_depend>
    - <exec_depend>mssage_runtime</exec_depend>
  
- CMakeLists.txt 수정
  - /xycar_ws/src/msg_send 아래에 있음
  
- Custom Message 설정과 확인
  - $ cm
  - $ rosmsg show my_msg로 결과 확인
  
- 내 코드 안에서 Custom Message 사용하기
  - 코드 안에 include or import 하는 법
    - (python) from msg_send.msg import my_msg
  
  - 다른 패키지에서도 custom msg 사용 가능
  - http://wiki.ros.org/ROS/Tutorials/CreatingMsgAndSrv
  
- my_msg 사용 예제
  - 샘플 파이썬 코드: 메시지 발행 Publisher 노드
  - $ gedit msg_sender.py
  - $ gedit msg_receiver.py
  - $ cm
  - $ roscore
  - $ rosrun msg_send msg_receiver.py
  - $ rosrun msg_send msg_sender.py

# 다양한 상황에서의 노드 통신
- 어떤 상황이 발생하고 어떤 문제, 해결방안은?
  - 누락 없이 모두 잘 도착하는지? (특히 처음과 끝)
  - 데이터 크기에 따른 전송속도는 어떻게 되는가?
  - 도착하는 데이터를 미처 처리하지 못하면 어떻게 되는가?
  - 주기적 발송에서 타임슬롯을 오버하면 어떻게 되는가?
  - 협업해야 하는 노드를 순서대로 기동시킬 수 있는가?