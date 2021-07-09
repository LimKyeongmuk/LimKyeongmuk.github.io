---
title: "ROS 원격 노드 통신"
excerpt: ""

categories:
  - ROS
tags:
  - ROS
---
# ROS 노드간 원격 통신
- ROS에서 노드와 노드가 서로 통신하면서 협업
  - 통신 프로토콜(TCP/IP 계열)
    - XMLRPC 프로토콜: 노드들이 마스터와 통신할 때 이용
    - TCPROS 프로토콜: 각 노드간 통신할 때 이용

- 하나의 장치 안에서 여러 노드가 서로 통신
  - 노드는 OS의 도움을 받아 하드웨어 장치들을 제어함
  - 노드들은 마스터의 도움을 받아서 서로 메시지를 주고 받음
  
- ROS에서 노드와 노드가 서로 통신하면서 협업
  - 서로 다른 하드웨어 사이에서 네트워크로 통신
  - 단일 하드웨어 안에 있는 노드끼리도 네트워크로 통신(통일성을 유지하기 위해서)

# 원격 통신 예제 구동
- ROS 원격 통신
  - PC (publisher: turtle_teleop_key)
  - Xycar (Subscriber: turtlesim_node)

  - 마스터의 IP 주소를 만천하에 공개
  - 노드 1과 노드 2가 마스터에게 본인의 정보와 IP 주소를 알려줌
 
- ROS 원격통신 환경
  - 학생 1, 2, 3이 Xycar를 제어
  - 마스터 Core도 다른 PC에서 작동(한군데에서 작동하면 됨)
  
  - 학생1: 발행자 노드(turtle_teleop_key)
  - 학생2: 발행자 노드(turtle_teleop_key)
  - 학생3: 발행자 노드(turtle_teleop_key)
  - Xycar: 마스터Core, 구독자 노드(turtlesim_node)

- ROS 원격 통신을 위한 환경 설정
  - IP 주소 설정
    - $ sudo gedit ~/.bashrc(아래 내용 수정)
      - ROS_MASTER_URI = roscore가 구동되는 장치(Xycar)의 IP 주소
      - ROS_HOSTNAME = 내 PC/노트북의 IP 주소($ ifconfig 명령으로 알아낼 수 있음)
  
    - $ source .bashrc (수정한 내용을 시스템에 반영)
  
  - 돌려보기
    - $ rosrun turtlesim turtle_teleop_key
  
  - 노드 간 메시지 흐름 확인
    - 새로운 터미널 창을 열어서 작업
    - $ rostopic list
    - $ rosnode info /turtlesim
    - $ rostopic echo /turtle1/cmd_vel
    - $ rqt_graph

# 원격 통신 환경 구축
- 원격지 통신 구성
  - 2대의 PC가 있어야 함
  - 집과 실습실 PC를 연결할 때, 공유기를 거쳐서 INTERNET을 거쳐서 다시 공유기로 가서 연결
  - 공유기 쪽에 제대로 셋팅을 하지 않으면 네트워크 통신이 잘 안됨
  - 공인 IP: 전 세계에서 유일한 값으로 인정해주는 것
  - 사설 IP: 공인 IP에서 나눠준 IP(밖에서는 사용할 수 없음)
  - 사설 IP를 가지고 있는 여러 장치를 '포트 포워딩'을 이용하여 연결
  
- 작업환경 (집)
  - 공유기 A
    - Student에 DMZ 설정
    - 공인 IP: 12.34.56.78
  
  - Student
    - OS:~
    - ROS Version: ~
    - 사설 IP: ~
    - Hostname: ~
  
- 작업환경 (외부)
  - 공유기 B
    - Teacher에 대해서 1024~65000 범위 포트 포워딩
    - 공인 IP: 99.88.77.66
  
  - Student
    - OS:~
    - ROS Version: ~
    - 사설 IP: ~
    - Hostname: ~  

- 네트워크 환경 설정 작업순서 및 흐름
  1) 공유기 포트 포워드 설정
    - 공유기 제조사마다 설정 방법이 다르므로 공유기 매뉴얼을 보고 설정할 것
    - 포트 포워딩 설정할 대 포트 범위를 1024~65000로 하기
    - DMZ 설정할 때 ROS 원격 통신을 시도할 장치의 IP를 입력하는 것
      - DMZ: 공유기의 모든 포트를 특정 사설 IP에 포트 포워딩 하는 것
  
  2) /etc/hosts 파일 수정
    - Student 쪽 /etc/hosts 파일을 열어서 #remote network: Portable ISP router 수정(자동차 IP가 아니라 공유기 IP) 
    - Teacher 쪽 /etc/hosts 파일을 열어서 #remote network: Portable ISP router 수정

  3) ROS 환경 변수 설정
    - ROS 마스터(roscore)는 ROS 네트워크에서 반드시 하나만 실행되어야 함
    - Student의 관점에서 ROS Master에 접근하기 위해서는 roscore가 실행되고 있는 Teacher 컴퓨터가 붙어있는 공유기의 공인 IP 주소가 필요
    - ROS 환경 변수 세팅: ~/.bashrc 파일을 열어서 ROS_HOSTNAME을 주석 추가(Student, Teacher 모두)

  4) 실행 테스트
    - Student의 PC에서 Publish 해보기(Student 쪽의 키보드 입력이 Teacher 화면에 표시되면 OK)
    - Student(Pub): $ rostopic pub /chatter std_msgs/String hello
    - Teacher(Sub): $ rostopic echo /chatter
    - 토픽: /catter, 전송 데이터: hello
  
- 정리하기 
  - 공유기 포트포워드 설정
    - 포트포워드: 공유기에 밖에서 온 것을 PC에 보내주는 것
  - 컴퓨터 /etc/hosts 수정
  - 컴퓨터 .bashrc (ROS 환경변수) 수정
  - 
  
# 원격 통신 프로그래밍
- 노드간 원격 통신 구성
  - 2개의 노드를 만들어서 원격지에 놓고 통신하게끔 진행
  - 노드1: remote_student(192.168.0.AAA)
    - 토픽 전송: msg_to_xycar(my_msg)
  - 노드2: remote_teacher(192.168.0.BBB)
    - 토픽 전송: msb_from_xycar(my_msg)
  
- 사용할 패키지
  - 기존에 만든 msg_send 패키지에서 작업(/xycar_ws/src/msg_send)
  
- Custom Message 사용
  - my_msg 메시지 사용
  - (python) from msg_send.msg import my_msg
  
- ROS 원격 통신 환경
  - 학생1: remote_student.py(발행자, 구독자 노드)
  - 학생2: remote_student.py(발행자, 구독자 노드)
  - 학생2: remote_student.py(발행자, 구독자 노드)
  - Xycar: remote_teacher.py(발행자, 구독자 노드), 마스터 Core
  
  - 하나의 ROS 협업 시스템이기 때문에 노드의 명칭이 모두 달라야 함
    - 소스코드를 수정해서 이름을 다르게 함
    - roslaunch 파일에서 이름 수정
    - init node에서 anonymous=True 설정
  
- 원격통신 실습 1)
  - 학생들이 my_msg 타입의 데이터를 보내고 그걸 강사가 받아서 출력
  - 학생이 발행하는 msg_sender.py 코드 작성하기(정보 전송)
  - Xycar 쪽에서는 msg_receiver.py 코드 작성하기(정보 출력)
  - 결과는 학생들이 보낸 my_msg 타입의 메세지 내용을 화면에 출력됨
  
- 원격통신 실습 2)
  - 학생들이 my_msg 타입의 데이터를 보내고 그걸 강사가 받으면 이름을 String 문자열에 담아 회신, 학생들은 String 내용을 확인하고 출력함
  - 학생: msg_to_xycar
  - 강사: msg_from_xycar
  
# ROS 원격 통신 프로그래밍 실습
- 받은 토픽을 가공해서 보내기