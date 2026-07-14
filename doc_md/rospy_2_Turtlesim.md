## rospy_tutorial/ Tutorials/ Turtlesim



---

## 간단한 turtlesim의 거북이를 제어하는 패키지 `turtle_pkg`를 작성

**출처 :**  <http://wiki.ros.org/rospy_tutorials/Tutorials/WritingPublisherSubscriber>

**튜토리얼 레벨 :**  초급

**선수 학습 :**  ROS 튜토리얼

**빌드 환경 :**  catkin **/** Ubuntu 20.04 **/** Noetic

---

### 1. 패키지 생성

작업경로를 ```~/catkin_ws/src``` 로 변경 후,  ```catkin_create_pkg``` 명령으로  ```rospy``` 에 의존하는`turtle_pkg ` 패키지 생성.

```bash
$ cd ~/catkin_ws/src
$ catkin_create_pkg turtle_pkg rospy
```

생성된 ```turtle_pkg``` 패키지 폴더로 경로 변경 후, 폴더 내용 확인.

```bash
$ cd turtle_pkg
$ ls
CMakeLists.txt  package.xml  src
```

`turtlesim`패키지의 `turtle_teleop_key`와 같이 `turtlesim`의 거북이를 제어할 수 있는 노드 `remote_turtle.py`를 작성해보자.

### 2.  `turtlesim`의 거북이를 원운동 시키는 노드`circle_move.py`작성



코드 작성을 위해 `~/catkin_ws/src/turtle_pkg/src`로 작업경로를 변경한다.

```bash
cd ~/catkin_ws/src/turtle_pkg/src
```

`touch`명령으로 크기가 0인 파일 `circle_move.py`를 생성한다.

```bash
touch circle_move.py
```

`ls -al`명령으로 된 파일생성을 확인한다.

```bash
ls -al
total 8
drwxrwxr-x 2 gnd0 gnd0 4096  7월 14 09:06 .
drwxrwxr-x 3 gnd0 gnd0 4096  7월 13 16:36 ..
-rw-rw-r-- 1 gnd0 gnd0    0  7월 14 09:06 circle_move.py
```

`chmod +x example_pub.py`명령으로 생성된 `circle_move.py`파일에 실행 속성을 부여한다.

```bash
chmod +x circle_move.py
```

`ls -al`명령으로 된 실행 속성 부여 여부를 확인한다.

```bash
ls -al
total 8
drwxrwxr-x 2 gnd0 gnd0 4096  7월 14 09:06 .
drwxrwxr-x 3 gnd0 gnd0 4096  7월 13 16:36 ..
-rwxrwxr-x 1 gnd0 gnd0    0  7월 14 09:06 circle_move.py
```

`example_pub.py` 파일 편집을 위해 다음 명령을 실행한다.

```bash
gedit circle_move.py &
```



```python
#!/usr/bin/env python3

import rospy,os
from geometry_msgs.msg import Twist

class CircleMove:

    def __init__(self):
        rospy.init_node('circle_move', anonymous=True)
        
def main():
    try:
        node = CircleMove()
        pub = rospy.Publisher('/turtle1/cmd_vel', Twist, queue_size=10)
        tw = Twist()
        tw.linear.x = 1.25; tw.angular.z = 0.5
        rate = rospy.Rate(10) # 10hz
        while not rospy.is_shutdown():
            pub.publish(tw); rate.sleep()
    except (KeyboardInterrupt, rospy.ROSInterruptException):
        pass
    finally:
        print("Program terminated!")

if __name__ == "__main__":
    main()
```



### 3. 작성한 패키지 빌드

`catkin_make` 실행을 위해 작업 경로를 catkin workspace 로 사용하고 있는 `~/catkin_ws` 로 변경한다.

```bash
cd ~/catkin_ws
```

`catkin_make` 실행.

```bash
catkin_make
```

빌드작업으로 변경된  `~/catkin_ws/devel/setup.bash` 의 내용을 `source` 명령을 이용하여 반영시킨다.

```bash
source ./devel/setup.bash
```



### 4. `circle_move.py`구동

`roscore` 실행

```bash
roscore
```



`turtlesim` 패키지의 `turtlesim_node` 구동

```bash
rosrun turtlesim turtlesim_node
```



`circle_move.py`구동

```
rosrun turtle_pkg circle_move.py
```

![](./img/turtlesim2.png)

`turtlesim_node`의 거북이가 원운동하는 것을 확인한다.



[튜토리얼 목록](../README.md) 







