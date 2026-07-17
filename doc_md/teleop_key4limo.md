## How to Start Limo 로봇을 위한 키보드 원격제어노드 작성





---

## 키보드 원격제어노드 작성

**참조 :**  <http://wiki.ros.org/rospy_tutorials/Tutorials/WritingPublisherSubscriber>

**튜토리얼 레벨 :**  초급

**선수 학습 :**  ROS 튜토리얼

**빌드 환경 :**  catkin **/** Ubuntu 20.04 **/** Noetic

---

Limo로봇을 위한 사용자 ROS 패키지 `limo_pkg`를 만들고 Limo로봇을 키보드로 원격제어하는 `remote_limo.py`노드를 만들어보자. 



#### 1 패키지 생성

작업경로를 `~/catkin_ws/src`로 변경

```bash
cd ~/catkin_ws/src
```

`rospy`에 대해 의존성을 갖는 ROS 패키지 `limo_pkg`생성

```bash
catkin_create_pkg limo_pkg rospy
```

[**Turtlesim 제어 실습**](./rospy_2_Turtlesim.md) 때 만들어 둔 `turtle_pkg`에서 `remote_turtle.py`와 `getchar.py`파일을 `~/catkin_ws/src/limo_pkg/src`로 복사한다.

```bash
cp ~/catkin_ws/src/turtle_pkg/src/getchar.py ~/catkin_ws/src/limo_pkg/src/
```

```bash
cp ~/catkin_ws/src/turtle_pkg/src/remote_turtle.py ~/catkin_ws/src/limo_pkg/src
```

복사한 `remote_turtle.py`를 `remote_limo.py`로 파일명 변경.

```bash
mv ~/catkin_ws/src/limo_pkg/src/remote_turtle.py  ~/catkin_ws/src/limo_pkg/src/remote_limo.py
```

`~/catkin_ws/src/limo_pkg/src`의 내용확인

```bash
ls -al ~/catkin_ws/src/limo_pkg/src
```

```bash
ls -al ~/catkin_ws/src/limo_pkg/src
total 20
drwxrwxr-x 3 gnd0 gnd0 4096  7월 15 14:53 .
drwxrwxr-x 3 gnd0 gnd0 4096  7월 15 14:29 ..
-rw-rw-r-- 1 gnd0 gnd0 1118  7월 15 14:33 getchar.py
drwxrwxr-x 2 gnd0 gnd0 4096  7월 15 14:53 __pycache__
-rwxrwxr-x 1 gnd0 gnd0 2466  7월 15 14:51 remote_limo.py
```



#### 2. Limo 로봇 키보드 원격제어 코드 작성

` remote_limo.py` 편집

```bash
gedit ~/catkin_ws/src/limo_pkg/src/remote_tlimo.py
```

```python
#!/usr/bin/env python3

import rospy, os
from getchar import Getchar
from geometry_msgs.msg import Twist

MAX_LIN_SPD = 0.55
MIN_LIN_SPD = -0.55
MAX_ANG_SPD = 1.0
MIN_ANG_SPD = -1.0

LIN_SPD_STEP = 0.025
ANG_SPD_STEP = 0.020

msg = """
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
"""

class RemoteLIMO():

    def __init__(self):
        rospy.init_node("remote_limo")
        


def main():
    try:
        node = RemoteLIMO()

        pub = rospy.Publisher("/cmd_vel", Twist, queue_size=10)
        tw = Twist()
        kb = Getchar()
        rate = rospy.Rate(10)

        print(msg)

        while not rospy.is_shutdown():
            if kb.chk_stdin():
                ch = kb.getch()

                if ch == 'w':
                    if tw.linear.x + LIN_SPD_STEP <= MAX_LIN_SPD:
                        tw.linear.x = tw.linear.x + LIN_SPD_STEP
                    else:
                        tw.linear.x = MAX_LIN_SPD

                elif ch == 's':
                    if tw.linear.x - LIN_SPD_STEP >= MIN_LIN_SPD:
                        tw.linear.x = tw.linear.x - LIN_SPD_STEP
                    else:
                        tw.linear.x = MIN_LIN_SPD

                elif ch == 'a':
                    if tw.angular.z + ANG_SPD_STEP <= MAX_ANG_SPD:
                        tw.angular.z = tw.angular.z + ANG_SPD_STEP
                    else:
                        tw.angular.z = MAX_ANG_SPD

                elif ch == 'd':
                    if tw.angular.z - ANG_SPD_STEP >= MIN_ANG_SPD:
                        tw.angular.z = tw.angular.z - ANG_SPD_STEP
                    else:
                        tw.angular.z = MIN_ANG_SPD

                elif ch == ' ':
                    tw.linear.x = tw.angular.z = 0.0

                elif ch == 'q':
                    break

                print("linear.x = %.3f, angular.z = %.3f" % (tw.linear.x, tw.angular.z))

            pub.publish(tw)
            rate.sleep()
        tw.linear.x = tw.angular.z = 0.0;   pub.publish(tw)
    except (KeyboardInterrupt, rospy.ROSInterruptException):
        print("Program terminated!")
    finally:
        os.system("rostopic pub -1 /cmd_vel geometry_msgs/Twist '{linear: {x: 0.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}'")

if __name__ == "__main__":
    main()
```



#### 3. 패키지 빌드

​	빌드작업을 위해 작업경로를 `cd ~/catkin_ws`로 변경

```bash
cd ~/catkin_ws
```

빌드

```
catkin_make
```

새로 빌드된 패키지 정보 반영

```
source ~/catkin_ws/devel/setup.bash
```



##### 5. `remote_limo.py` 동작 테스트

새 터미널을 열어 `roscore` 실행

```
roscore
```

새 터미널을 열어 Limo 로봇에 `ssh` 연결 `ssh wego@[Limo 로봇 IP주소]` Limo 로봇의 IP주소가 `10.42.0.3`일 경우,

```bash
ssh wego@10.42.0.3
```

`ssh`원격 연결창에서 `limo_bringup`패키지의 `limo_start.launch` 구동

```
rosrun limo_bringup limo_start.launch
```

```bash
roslaunch limo_bringup limo_start.launch 
... logging to /home/wego/.ros/log/bf20c304-800d-11f1-93b4-23a5b90f8599/roslaunch-wego-robotics-2955.log
Checking log directory for disk usage. This may take a while.
Press Ctrl-C to interrupt
Done checking log file disk usage. Usage is <1GB.

started roslaunch server http://10.42.0.3:45715/

SUMMARY
========

PARAMETERS
 * /limo_base_node/base_frame: base_link
 * /limo_base_node/odom_frame: odom
 * /limo_base_node/port_name: ttyTHS1
 * /limo_base_node/pub_odom_tf: 
 * /limo_base_node/use_mcnamu: False
 * /rosdistro: melodic
 * /rosversion: 1.14.12
 * /ydlidar_node/angle_max: 90.0
 * /ydlidar_node/angle_min: -90.0
 * /ydlidar_node/auto_reconnect: True
 * /ydlidar_node/baudrate: 115200
 * /ydlidar_node/frame_id: laser_link
 * /ydlidar_node/frequency: 8.0
 * /ydlidar_node/ignore_array: 
 * /ydlidar_node/isSingleChannel: True
 * /ydlidar_node/port: /dev/ydlidar
 * /ydlidar_node/range_max: 12.0
 * /ydlidar_node/range_min: 0.1
 * /ydlidar_node/resolution_fixed: True
 * /ydlidar_node/reversion: True
 * /ydlidar_node/samp_rate: 3

NODES
  /
    base_link_to_camera_link (tf/static_transform_publisher)
    base_link_to_imu_link (tf/static_transform_publisher)
    base_link_to_laser_link (tf/static_transform_publisher)
    limo_base_node (limo_base/limo_base_node)
    ydlidar_node (ydlidar_ros/ydlidar_node)

ROS_MASTER_URI=http://10.42.0.1:11311

process[limo_base_node-1]: started with pid [2966]
process[ydlidar_node-2]: started with pid [2967]
process[base_link_to_camera_link-3]: started with pid [2968]
__   ______  _     ___ ____    _    ____  
\ \ / /  _ \| |   |_ _|  _ \  / \  |  _ \ 
 \ V /| | | | |    | || | | |/ _ \ | |_) | 
  | | | |_| | |___ | || |_| / ___ \|  _ <  
  |_| |____/|_____|___|____/_/   \_\_| \_\ 

process[base_link_to_imu_link-4]: started with pid [2969]
process[base_link_to_laser_link-5]: started with pid [2973]
[ INFO] [1784093297.799046102]: open the serial port: /dev/ttyTHS1
[ INFO] [1784093297.844127204]: [YDLIDAR INFO] Now YDLIDAR ROS SDK VERSION:1.4.6 .......
YDLidar SDK initializing
YDLidar SDK has been initialized
[YDLIDAR]:SDK Version: 1.4.7
LiDAR successfully connected
[YDLIDAR]:Lidar running correctly ! The health status: good
LiDAR init success!
[YDLIDAR]:Fixed Size: 420
[YDLIDAR]:Sample Rate: 4K
[YDLIDAR INFO] Current Sampling Rate : 4K
[YDLIDAR INFO] Now YDLIDAR is scanning ......
```



노트북 PC에서 새 터미널을 열고 `limo_pkg`패키지의 `remote_limo.py`노드 구동

```bash
rosrun limo_pkg remote_limo.py
```

```bash
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

linear.x = 0.000, angular.z = 0.000
```



`w`, `s`, `a`, `d`, `SPACE`키를 이용하여 Limo 로봇 원격제어 테스트 수행.

[튜토리얼 목록](../README.md) 







