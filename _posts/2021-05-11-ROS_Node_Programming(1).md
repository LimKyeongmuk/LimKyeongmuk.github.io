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
    - 1~100까지 숫자를 보냄
    - 받는 Receiver에서 출력
    - 빠지는 것이 있는지 확인해보기
    - 파이썬 파일 2개와 런치파일 1개 만들기
      - sender_serial.py
      - receiver_serial.py
      - sr_serial.launch
    
    - 숫자를 보내서 받는 쪽이 누락된 것이 있는지 쉽게 알수 있도록 1, 2, 3, 4, ... 숫자를 보냄
    - 화면에 출력해서 확인하던가 숫자가 받은 쪽에서 다음 숫자가 나오는 것이 맞는지 확인해보기
    - 보내는 쪽이 안보낸것인지, 받는 쪽이 못 받은 것인지 구분해보는 것도 고려해보기
    - 중간보다는 맨 처음과 끝에서 누락되는지 확인보내기
    - 받는 쪽을 먼저 실행시켜 놓고, 그 다음에 보내는 쪽을 실행시켜야 하는데 roslaunch는 노드를 순서대로 실행시킬 수 없으므로 rosrun을 사용해보기
    - Publisher와 Subscriber의 실질적인 통신 구축에 지연시간 존재
    - 받을 사람이 준비가 안됐는데 물건을 던지면 못받고 잃어버림
  
  - 데이터 크기에 따른 전송속도는 어떻게 되는가?
    - 1byte씩 보내는 것을 측정해보기
    - 100만byte씩 보내는 것을 측정해보기
    - 노트북의 최대 전송속도하기
    - 최대 크기를 1M 이하로 하는 것이 ROS 시스템에 가장 가성비 좋음
    - 최소 한 byte씩 보내는 것이 좋음
    - 1G 짜리 영화 보내보기 100개 보내보기
    - 파이썬 파일 2개랑 런치파일 1개 만들기
      - sender_speed.py
      - receiver_speed.py
      - sr_speed.launch
    
    - 정해진 크기의 데이터를 반복해서 왕창 보내기
    - 보내는 쪽은 10분 동안 시간을 정해놓고 쉴 새 없이 보내기
    - 10분 동안 몇 바이트 보냈는지 체크해서 송신속도를 계산함
    - 받는 쪽도 10분 동안 시간을 정해놓고, 모두 얼마나 받았는지 체크해서 수신속도 계산해보기
    - 단위는 Kbytes/sec
    - 다양하게 1byte 10byte 100byte로 잘라서 보냈을때 어느정도 했을 때 가성비가 제일 좋음(엑셀 그래프)
    - 매우 큰 동영상도 보내보면서 테스트 해보기
    - 받는 쪽이 없으면 어떻게 될지(송신속도가 더 빨라질까, 상관 없을까)
    
  - 도착하는 데이터를 미처 처리하지 못하면 어떻게 되는가?
    - 없어지는지, 시간이 오래걸려도 해결하는지, 순서가 바뀌는지 찾아보기
    - ROS 커뮤니티에서 만든 받는 함수 코드의 소스코드 보기
    - 파이썬 파일 2개랑 런치파일 1개 만들기
      - sender_overflow.py
      - receiver_overflow.py
      - sr_overflow.launch
    
    - 받는 쪽이 버벅되게 만들어 놓고 많은 데이터를 보내보기
    - 버벅되게 만들기: 토픽이 도착하면 콜백함수가 작동하는데 구독자의 콜백함수 안에 시간이 많이 걸리는 코드를 넣어서 토픽 처리에 시간이 걸리도록 만들기(안에 loop 넣어도됌)
    - 콜백 함수가 끝나지 않았는데 토픽이 새로 도착하면 어떻게 되는지
      - 도착한 토픽은 임시로 어딘가 쌓이는가? 그걸 나중에 꺼내서 처리할 수 있는가?
      - 아니면 그냥 없어지는가? 한 번 못받은 토픽은 영영 못 받는 것인가?(history가 반드시 추적이 되어야만 하는 데이터가 있음)
      - 발행자는 이 사실을 아는가? 알려줄 수 있는 방법은 있는가?
    
  - 주기적 발송에서 타임슬롯을 오버하면 어떻게 되는가?
    - 파이썬 파일 2개랑 런치파일 1개 만들기
      - sender_timeslot.py
      - receiver_timeslot.py
      - sr_timeslot.launch
    
    - 1초에 5번 반복하게 하고 작업시간이 0.2초가 넘게 만들어보기
      - Rate(5) 세팅하고 sleep() 앞에 들어간 작업코드에 대해서 수행시간을 늘려보기
      - 늘렸다 줄었다 변동성 있게 해보기. 입력값을 받아서 조정할 수 있도록 만들기
    
    - 1초에 5번 규칙을 지킬 수 없으면 어떻게 할까?
      - 앞에서부터 쭉 밀리는 식으로 일할까?
      - 쉬는 시간을 조정할까?
      - 이번엔 3번만 하고 다음번을 기약할까?
  
    - rospy.Rate(5) & rospy.sleep() 조사해보기
      - 한 타임슬로에서 한 번의 job과 Rate/sleep()이 발생하는 것이 기본
      - 주어진 타임 슬롯에서 자신이 할 일을 마치고 시간이 남으면 잠시 휴식 시간을 갖음
      - rate.sleep은 자신에게 주어진 전체 수행 시간을 알고 있음
  
    - 토픽을 발행하는 예제 파이썬 코드 teacher_int32_job.py 작성
      - teacher_int32.py를 복사해서 이름을 수정하여 사용하면 편리함
      - student_int32.py는 기존 파일 이용
      - 토픽 이름은 'msg_to_student', 메시지 타입은 int32
      - 0.2초씩 다섯 차례 토픽을 보냄(따라서 총 수행시간은 1초가 됨)
      - 키보드 입력을 받던지, Launch parameter를 이용해서 1차례 보낼 양을 세팅
      - 시간함수 time.time()을 이용하여 각 슬롯간 소요시간과 5개의 전체 슬롯의 소요시간을 계산하여 출력
    
  - 협업해야 하는 노드를 순서대로 기동시킬 수 있는가?
    - rosrun에서 따로 수행하지 않고 roslaunch에서 순서대로 할 수 있는 방법을 생각해보기
    - 파이썬 파일 5개랑 런치파일 1개 만들기
      - first.py second.py third.py fourth.py receiver.py
      - sr_order.launch
  
    - 순서대로 receiver에 메시지를 보내도록 만들기
      - receiver는 도착한 순서대로 출력(First-Second-Third-Fourth 이 순서로)
      - First가 보냈다고 말해야 하는지, Second가 First가 보내야하는지 좋을지 생각해보기
      - 순서를 정하는 것에서 간단해야 함
        
    - 어떻게 동기를 맞추고 순서를 맞출 수 있을까?
      - Launch 파일을 이용해서 할 수 있을까?
      - ROS의 도움으로 할 수 있을까?
      - 아니면 별도의 코드를 내가 프로그래밍 해야 할까?
- 과제
  - 5가지 문제에 해새 현상확인과 원인분석, 해결책 적용결과를 정리한 기술문서 PPT 제출
  - 추가로 2개씩, '이런 상황에는 어떻게 동작할까?' 문제를 내고 현상확인과 원인분석, 해결책 적용결과를 정리한 기술문서 PPT 제출
  
