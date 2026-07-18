## Limo 로봇 Gazebo 시뮬레이션





---

Gazebo는 ROS에서 제공하는 물리 시뮬레이터로, 로봇의 움직임, 센서, 물리 효과를 실제와 매우 유사하게 재현해 줌으로 실제 로봇이나 환경 없이도 개발할 수 있게 해주며 현실적인 문제로 실제 실험하기 어려운 자율주행 , 충돌 테스트 등을 해 볼 수 있게 해준다.

Gazebo는 크게 `World`, `Robot`, `Physics Engine`의 3가지 영역으로 구성된다.

```
Gazebo
├── World(환경)
│   ├── 바닥
│   ├── 벽
│   ├── 장애물
│   └── 조명
│
├── Robot(로봇)
│   ├── 바퀴
│   ├── 링크(Link)
│   ├── 조인트(Joint)
│   └── 센서
│
└── Physics Engine
    ├── 중력
    ├── 마찰
    ├── 충돌
    └── 관성
```

Limo 로봇 Gazebo 시뮬레이션을 구동해보자.

---

#### 1. PC에 limo Gazebo 시뮬레이션 패키지 설치

`~/catkin_ws/src`로 작업경로 변경

```bash
cd ~/catkin_ws/src
```

 **limo_gazebo_sim 패키지 소스코드 복제**

```bash
git clone https://github.com/greattoe/limo_gazebo_sim.git
```

**패키지 빌드**

```bash
cd ~/catkin_ws && catkin_make
```

새로 빌드된 패키지 정보 반영

```
source ~/catkin_ws/devel/setup.bash
```

구동할 수 있는 `launch`파일 찾아보기

```
l ~/catkin_ws/src/limo_gazebo_sim/launch/
total 16
-rwxrwxrwx 1 gnd0 gnd0 1848  7월  3 03:56 limo_ackerman.launch
-rwxrwxrwx 1 gnd0 gnd0 1751  7월  3 03:56 limo_four_diff_empty_world.launch
-rwxrwxrwx 1 gnd0 gnd0 1792  7월 16 06:16 limo_four_diff_turtlebot3world.launch
-rwxrwxrwx 1 gnd0 gnd0 3873  7월 11 23:16 limo_gmapping.launch
```

` limo_four_diff_turtlebot3world.launch`구동

```
roslaunch limo_gazebo_sim limo_four_diff_turtlebot3world.launch
```

![](./img/limo_gazebo_turtlebot3world.png)

![](./img/limo_rviz_turtlebot3world.png)

`rostopic list`명령으로 Limo Gazebo 시뮬레이터가 관련 토픽 확인

```
rostopic list
```

```
rostopic list
/camera_ir/parameter_descriptions
/camera_ir/parameter_updates
/clicked_point
/clock
/cmd_vel
/gazebo/link_states
/gazebo/model_states
/gazebo/parameter_descriptions
/gazebo/parameter_updates
/gazebo/performance_metrics
/gazebo/set_link_state
/gazebo/set_model_state
/gazebo_ros_control/pid_gains/front_left_wheel/parameter_descriptions
/gazebo_ros_control/pid_gains/front_left_wheel/parameter_updates
/gazebo_ros_control/pid_gains/front_right_wheel/parameter_descriptions
/gazebo_ros_control/pid_gains/front_right_wheel/parameter_updates
/gazebo_ros_control/pid_gains/rear_left_wheel/parameter_descriptions
/gazebo_ros_control/pid_gains/rear_left_wheel/parameter_updates
/gazebo_ros_control/pid_gains/rear_right_wheel/parameter_descriptions
/gazebo_ros_control/pid_gains/rear_right_wheel/parameter_updates
/initialpose
/joint_states
/limo/color/camera_info
/limo/color/image_raw
/limo/color/image_raw/compressed
/limo/color/image_raw/compressed/parameter_descriptions
/limo/color/image_raw/compressed/parameter_updates
/limo/color/image_raw/compressedDepth
/limo/color/image_raw/compressedDepth/parameter_descriptions
/limo/color/image_raw/compressedDepth/parameter_updates
/limo/color/image_raw/mouse_click
/limo/color/image_raw/theora
/limo/color/image_raw/theora/parameter_descriptions
/limo/color/image_raw/theora/parameter_updates
/limo/depth/camera_info
/limo/depth/image_raw
/limo/depth/image_raw/mouse_click
/limo/depth/points
/limo/imu
/limo/scan
/move_base_simple/goal
/odom
/rosout
/rosout_agg
/tf
/tf_static
```

Gazebo 시뮬레이션을 사용할 경우 `roscore` , `limo_bringup`패키지의 `limo_start.launch`를 구동하지 않아도 된다. 위 `rostopic list`명령 실행 결과를 보면, 이미 필요한 토픽들이 모두 시뮬레이션 된 것을 볼 수 있다.

이제 [Limo로봇 키보드 원격제어 노드 작성](./teleop_key4limo.md) 실습에서 작성한 `limo_pkg`의 `remote_limo.py`노드를 구동하여 시뮬레이션 된 limo로봇을 제어해보자.

```
rosrun limo_pkg remote_limo.py
```

```
rosrun limo_pkg remote_limo.py 

==========================
 LIMO Keyboard Teleop
==========================

w : linear.x +0.025
s : linear.x -0.025

a : angular.z +0.020
d : angular.z -0.020

SPACE : Stop

CTRL+C : Quit
==========================

linear.x = 0.025, angular.z = 0.000
linear.x = 0.050, angular.z = 0.000
linear.x = 0.075, angular.z = 0.000
linear.x = 0.000, angular.z = 0.000
linear.x = 0.000, angular.z = 0.020
linear.x = 0.000, angular.z = 0.040
linear.x = 0.000, angular.z = 0.020
linear.x = 0.000, angular.z = 0.000
```

시뮬레이션 화면의 Limo 로봇이 제어되는 것을 확인한다.



[튜토리얼 목록](../README.md) 





