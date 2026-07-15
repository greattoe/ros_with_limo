## rospy_tutorial/ Tutorials/ WritingPublisherSubscriber



---

## 간단한 퍼블리셔와 서브스크라이버 작성 

**출처 :**  <http://wiki.ros.org/rospy_tutorials/Tutorials/WritingPublisherSubscriber>

**튜토리얼 레벨 :**  초급

**선수 학습 :**  ROS 튜토리얼

**빌드 환경 :**  catkin **/** Ubuntu 20.04 **/** Noetic

---

### 1. 패키지 생성

작업경로를 ```~/catkin_ws/src``` 로 변경 후,  ```catkin_create_pkg``` 명령으로  ```rospy``` 에 의존성을 가진 ```rospy_tutorial``` 패키지 생성.

```bash
$ cd ~/catkin_ws/src
$ catkin_create_pkg rospy_tutorial rospy
```

생성된 ```rospy_tutorial``` 패키지 폴더로 경로 변경 후, 폴더 내용 확인.

```bash
$ cd rospy_tutorial
$ ls
CMakeLists.txt  package.xml  src
```

`CMakeLists.txt`와 `package.xml`파일은 `catkin_make` 실행 시 빌드 관련 설정 파일들로 `roscpp`로 작업할 경우에는 엄격한 설정 편집이 필요하지만 `rospy`로 작업할 경우 상대적으로 특수한 경우(사용자 정의 메시지 형식을 사용하는 경우 등)가 아니고서는 자동 생성된 값을 그대로 적용할 수 있다. 

패키지 폴더에 자동 생성된 `src` 폴더에 해당패키지를 구성하는 의 개별 노드 코드를 작성해 넣는다.

### 2. package.xml 파일과 CMakeList.txt 확인(편집)

파이썬 스크립트를 이용하여 패키지를 작성할 경우  ```package.xml``` 파일과 ```CMakeList.txt``` 파일에서 확인할 내용은 다음과 같다. ( 자동으로 해당 내용이 작성되어 있지만, 그 내용이 몇 번 째 줄에 적혀있는 지 라도 한 번 확인해두자. )

#### 2.1 package.xml

```package.xml``` 파일은 다음 내용을 반드시 포함하고 있어야 한다. 

```xml
<buildtool_depend>catkin</buildtool_depend>
```

#### 2.2 CMakeList.txt

```CMakeList.txt``` 파일은 최소한 다음 내용을 반드시 포함하고 있어야 한다. 

```bash
cmake_minimum_required(VERSION 2.8.3)
project(rospy_tutorial)

find_package(catkin REQUIRED COMPONENTS rospy)
catkin_package()
```



### 3. 토픽 퍼블리셔 노드`example_pub.py`작성



코드 작성을 위해 `~/catkin_ws/src/rospy_tutorial/src`로 작업경로를 변경한다.

```bash
cd ~/catkin_ws/src/rospy_tutorial/src
```

`touch`명령으로 크기가 0인 파일 `example_pub.py`를 생성한다.

```bash
touch example_pub.py
```

`ls -al`명령으로 된 파일생성을 확인한다.

```bash
ls -al
total 8
drwxrwxr-x 2 gnd0 gnd0 4096  7월 14 09:06 .
drwxrwxr-x 3 gnd0 gnd0 4096  7월 13 16:36 ..
-rw-rw-r-- 1 gnd0 gnd0    0  7월 14 09:06 example_pub.py
```

`chmod +x example_pub.py`명령으로 생성된 `example_pub.py`파일에 실행 속성을 부여한다.

```bash
chmod +x example_pub.py
```

`ls -al`명령으로 된 실행 속성 부여 여부를 확인한다.

```bash
ls -al
total 8
drwxrwxr-x 2 gnd0 gnd0 4096  7월 14 09:06 .
drwxrwxr-x 3 gnd0 gnd0 4096  7월 13 16:36 ..
-rwxrwxr-x 1 gnd0 gnd0    0  7월 14 09:06 example_pub.py
```

`example_pub.py` 파일 편집을 위해 다음 명령을 실행한다.

```bash
gedit example_pub.py &
```



```python
#!/usr/bin/env python3

import rospy
from std_msgs.msg import String

class ExamplePub:

    def __init__(self):
        rospy.init_node("example_pub", anonymous=True)

def main():
    try:
        node = ExamplePub()
        pub = rospy.Publisher('hello', String, queue_size=10)
        msg_str = String()
        rate = rospy.Rate(1) # 1hz(1time/sec)
        
        while not rospy.is_shutdown():
            msg_str.data = "hello world %s" % rospy.get_time()
            pub.publish(msg_str)
            rate.sleep()

    except (KeyboardInterrupt, rospy.ROSInterruptException):
        pass
    finally:
        print("Program terminated!")

if __name__ == "__main__":
    main()
```





### 4. 작성한 패키지 빌드

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



### 5. `example_pub.py`구동

`roscore` 실행

```bash
roscore
```

 `example_pub.py` 노드는 `hello`라는 이름의 문자열 토픽을 발행한다. 따라서 해당 노드 구동 전, `hello`라는 이름의 토픽 존재 여부 파악을 위해 `rostopic list`명령을 실행한다.

```bash
rostopic list
/rosout
/rosout_agg
```

현재는 `hello`토픽이 존재하지 않음을 확인할 수 있다.

`Ctrl+Alt+T` 를 입력하여 새창을 열고 `rosrun rospy_tutorial example_pub.py`명령으로 `example_pub.py` 노드를 실행한다.

```bash
$ rosrun rospy_tutorial example_pub.py
```



`Ctrl+Alt+T` 를 입력하여 새창을 열고 `rostopic list`명령을 실행한다.

```bash
rostopic list 
/hello
/rosout
/rosout_agg
```

`hello`토픽을 확인할 수 있다.

토픽 내용 확인을 위해 `rostopic echo /hello`명령을 실행한다.

```bash
rostopic echo /hello 
data: "hello world 1783995803.3572793"
---
data: "hello world 1783995803.4575667"
---
data: "hello world 1783995803.5576196"
---
data: "hello world 1783995803.6576245"
---
data: "hello world 1783995803.7576108"
---
data: "hello world 1783995803.857637"
---
data: "hello world 1783995803.9576106"
```



### 6. 토픽 서브스크라이버 노드`example_pub.py`작성

`~/catkin_ws/src/rospy_tutorial/src`로 작업경로 변경

```bash
~/catkin_ws/src/rospy_tutorial/src
```

크기가 0인 `example_sub.py`파일 생성

```bash
touch example_sub.py
```

`example_sub.py`파일에 실행속성 부여

```bash
chmod +x example_sub.py
```

`example_sub.py`파일에 실행속성 부여 여부 확인

```bash
ls -al
total 16
drwxrwxrwx 2 gnd0 gnd0 4096  7월 14 10:54 .
drwxrwxrwx 3 gnd0 gnd0 4096  7월  2 13:06 ..
-rwxrwxrwx 1 gnd0 gnd0  718  7월 14 10:54 example_pub.py
-rwxrwxrwx 1 gnd0 gnd0  529  7월  9 01:45 example_sub.py
```



`example_sub.py`파일 편집

```
gedit example_sub.py &
```

```python
#!/usr/bin/env python3

import rospy
from std_msgs.msg import String

class ExampleSub:

    def __init__(self):
        rospy.init_node("example_sub", anonymous=True)
        rospy.Subscriber("/hello", String, self.callback)
        
    def callback(self, msg):
        print("%s" %msg.data)

def main():
    try:
        node = ExampleSub()
        rospy.spin()

    except (KeyboardInterrupt, rospy.ROSInterruptException):
        pass
    finally:
        print("Program terminated!")

if __name__ == "__main__":
    main()
```









### 4. rospy_tutorial 패키지 빌드

`rospy_tutorial` 패키지에 ` example_sub.py`노드가 추가되었으므로 `rospy_tutorial` 패키지를 다시 빌드해야한다. 

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



### 5. `example_pub.py`구동

`roscore`, `example_pub.py`가 구동된 상태에서 `example_sub.py`노드를 구동한다. 

```bash
rosrun rospy_tutorial example_sub.py
```

```bash
rosrun rospy_tutorial example_sub.py 
hello world 1783998222.9638186
hello world 1783998223.0637865
hello world 1783998223.1637855
hello world 1783998223.2639637
hello world 1783998223.3639643
hello world 1783998223.4639268
hello world 1783998223.5639706
hello world 1783998223.6639717
hello world 1783998223.7637818
hello world 1783998223.8639686
hello world 1783998223.9639366
```





[튜토리얼 목록](../README.md) 







