---
title: "자이카 라이다 활용"
excerpt: ""

categories:
  - Xycar
tags:
  - Xycar
  - Lidar
---
# 라이다 센서 활용
### 라이다 센서를 위한 ROS 노드와 토픽
- 라이다
  - 1 채널, 2D 라이다
  - 1도, 3600~4000 샘플링, 15cm~12m Range

- 라이다를 위한 ROS Package
  - xycar_ws > src > xycar_lidar
  
- 라이다의 관련 노드와 토픽
  - /xycar_lidar 노드에서 발행하는 /scan 토픽을 이용
  
- /scan 토픽
  - 타입 = sensor_msgs/LaserScan
  - 구성
    - 헤더(시퀀스 번호, 시간, 아이디 정보)
    - angle_min, max:
    - ranges: 장애물까지의 거리정보가 담김
    - intensities: 강도, 특성(딱딱한지 말랑한 물체인지)
  
### 라이다를 이용해 장애물까지의 거리정보 출력
- 패키지 생성
- 소스 파일 작성
- 런치 파일 작성
- rqt_graph로 확인하기

# 라이다 데이터 시각화
### RVIZ에서 라이다 센싱 데이터 시각화
- 패키지 생성(rviz_lidar)

- 라이다 장치가 없는 경우
  - 실제 라이다 장치를 대신해서 /scan 토픽을 발행하는 프로그램을 이용
  - ROS에서 제공하는 "rosbag" 이용
  - 라이다에서 발행하는 /scan 토픽을 저장해 놓은 파일을 사용해서 그 당시의 시간 간격에 맞추어 /scan 토픽을 발행할 수 있음
  - 디버깅이나 연구 개발용으로 매우 좋게 사용할 수 있음
  
- 라이다 Viewer - 플러그인 추가
  - RVIZ 우측 Displays 탭 하단에 Add 버튼을 클릭한 후 LaserScan 선택하고 OK 버튼을 클릭
  
- 라이다 Viewer - Topic 설정
  - 라이다의 데이터는 /scan 토픽으로 publisher 되고 있으므로 Topic 칸에 /scan 입력
  
- 라이다 Viewer - Fixed Frame
  - Fixed Frame에 기존 'map' 대신에 'laser' 입력
  
- 라이다 Viewer - Size
  - Size(m): Size 부분을 0.1 정도로 변경하면 화면에 빨간색 점이 잘 보임
  
- 결과 확인
  - rqt_graph
  
# RVIZ 기반 라이다 뷰어 제작
### rosbag 사용법
- ROSBAG(ROS 명령어)
  - 토픽을 구독하여 파일로 저장하거나, 파일에서 토픽을 꺼내 발행하는 기능
  - rosbag은 /scan 토픽을 구독하여 파일로 저장함
  - rosbag은 토픽의 내용뿐만 아니라 시간도 적어놓기 때문에 나중에 파일에서 정보를 읽어 /scan 토픽을 발행함
  
- rosbag 사용법
  - $rosbac record -O lidar_topic scan
  - $rosbag play lidar_topic.bag
  - 런치파일에서 node 선언
  
- rosbag 저장 파일
  - /scan 토픽이 저장된 .bag 파일
  - lidar_topic.bag
  
### RANGE 메시지를 RVIZ에서 시각화 하는 방법
- RANGE 데이터를 발행해서 뷰어에 표시하기
  - 토픽 전달 흐름
    - lidar_range.py라는 파일로 Range 데이터를 담은 토픽을 발행
    - /scan1 이름의 토픽을 발행
    - RVIZ 뷰어로 실행
  
  - Range 타입의 데이터를 담은 /scan1, /scan2, /scan3, /scan4의 4개 토픽을 발행
  - RVIZ에서는 원뿔 그림으로 Range 거리 정보를 시각화 하여 표시
  
- 파일 구성
  - rviz_lidar 패키지에서 작업
  
- 노드 연결 관계
  - 보내는 노드: lidar_range
  - 받는 노드: RVIZ
  - 토픽 이름: /scan1, /scan2, /scan3, /scan4
  - 메시지 타입: RANGE(from snesor_msgs.msg import Range)
  
- 토픽을 Publish 하는 노드 만들기
  - /src 폴더 아래에 lidar_range.py 작성
  - 4개의 publisher를 선언하고 모두 각각 다른 토픽 이름으로 발행하도록 작성
  
- 발행하는 토픽의 데이터를 Range 타입으로 설정
  - 메시지 구조 확인
    - $rosmsg show sensor_msgs/Range
  
- Range 메시지
  - http://docs.ros.org에서 찾아보기
  
- 토픽 발행 확인
  - 터미널 창을 열어서 현재 데이터가 알맞게 전송 중인지 확인
    - $ rostopic list
    - $ rostopic echo scan1
  
- Range 뷰어 설정 - Fixed Frame 지정
  - Publisher 노드에서 "sensorXY"로 수정
  
- Range 뷰어 설정 - 플러그인 추가
  - RVIZ 우측 Display 탭 하단에 Add 버튼을 누른 후 By topic 탭에서 /scan# 아래 Range를 선택하고 OK 버튼 클릭
  - 여러 개가 한 번에 선택되지 않으므로, 하나씩 선택함
  
- Range 뷰어 색상 지정
  - Color 지정
  - RVIZ Display Tab
  
- Range 뷰어 설정 - 표시 설정
  - 좌측 Displays 탭의 4개의 Range 중 시각화 할 토픽의 선택 가능
  - 4개를 모두 선택할 경우, 겹쳐서 표시되기 때문에 확인하기 어려움
  
- lidar_range.rviz 파일 저장
  - RVIZ 창을 닫으려고 하면 설정값을 물어봄
  
### RVIZ에서 라이다 데이터 뷰어 제작하기
- 과제 목표
  - RVIZ에서 라이다 정보를 Range로 표시하기
  - 라이다 데이터를 Range 데이터로 바꿔서 RVIZ로 표시하기
  - 가운데 직육면체를 그리고 상하좌우에 겹치지 않게 원뿔 모양을 그리기
  
- 데이터 전달 흐름
  - rosbag 파일(lidar_topic.bag)에서 scan 토픽 발행(메시지 타입 LaserScan)
  - lidar_urdf.py가 scan 토픽을 받아서 새로운 토픽(scan1, scan2, scan3, scan4) 만들어 발행(메시지 타입 Range)
  - RIVZ에서는 Range 형식로 거리정보를 시각화하여 보여줌
  
- 파일 구성
  - 패키지: rviz_lidar
  - 런치 파일: lidar_urdf.launch
  - 파이썬 파일: lidar_urdf.py
  - 모델링 URDF 파일: lidar_urdf.urdf(납작한 직육면체)
  - RVIZ 파일: lidar_urdf.rviz
  
- 파이썬 파일
  - LaserScan 타입의 데이터를 Range 타입으로 변경
  - 'scan' 토픽 (360개의 ranges)을 받아 4개의 scan1~4 토픽을 발행
  - 받는 토픽: sensor _msgs/LaserScan
  - 발행 토픽: sensor _msgs/Range
  
- 파이썬 파일 - lidar_urdf.py

- URDF 작성하기(lidar_urdf.urdf)
  - World의 중앙에 RED 박스를 만들고 4방향에 센서 프레임을 연결함
  - base_link에 가로세로 20센치 RED 박스 baseplate를 만들어 연결
  - 센서는 x, y축을 기준으로 중심에서 10cm씩 이동시켜서 박스의 끝부분에 배치
  
- 전체 동작 흐름도