## How to Start Limo 로봇





---

## ROS 환경에서 Limo로봇 운영

**참조**

- **<https://github.com/agilexrobotics/limo_ros>**
- **<https://github.com/greattoe/ros_with_limo/blob/master/doc_pdf/Limo%20user%20manual(KO).pdf>**

**튜토리얼 레벨 :**  초급

**선수 학습 :**  ROS 튜토리얼

**개발 환경 :**  Ubuntu 20.04 **/** ROS Noetic

---

#### 0. 준비작업

- 이 후 Limo로봇의 방향기준은 카메라 방향을 전방, 모니터 방향을 후방으로 정한다.

- Limo로봇 충전 어댑터 연결(LIMO로봇의 모니터(터치스크린) 아래 후방 덮개를 열어 전원 어댑터를 연결한다.)
- Limo로봇을 위한 키보드 및 마우스 연결(LIMO로봇의 우측면 덮개를 열어 키보드와 마우스를 연결한다.)

- **PC와 Limo로봇의 네트워크 연결 수단**( 휴대폰 핫스팟 또는 전용으로 사용할 공유기 등)을 준비해 둔다.



#### 1. 전원켜기

좌측 바퀴 사이의 둥근 버튼이 전원 버튼이다. 이 버튼을 3~4초가량 충분히 길게 누르면 '삑'소리와 함께  Limo 로봇 후면의 터치스크린에 부팅 메세지가 출력되면서 Limo로봇의 젯슨나노 보드가 부팅된다. 부팅이 완료되면 Limo 로봇 후면의 터치스크린의 로그인 화면 password 입력란에 `wego`를 입력한다.



#### 2. 네트워크 연결

##### 2.1 Limo 로봇

앞서 Limo로봇에 연결한 키보드, 마우스와 Limo 로봇 후방의 터치스크린을 이용해 준비한 휴대폰 핫스팟에 연결 후 터미널을 열어 아래 명령으로 `openssh-server` 설치

```bash
sudo apt install openssh-server
```



##### 2.2 PC

역시 준비한 휴대폰 핫스팟에 연결 후 터미널을 열어 `nmap` 설치

```bash
sudo apt install nmap
```

`ifconfig`명령으로 할당된 IP주소 확인

```bash
ifconfig
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 2704  bytes 319199 (319.1 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2704  bytes 319199 (319.1 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

virbr0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255
        ether 52:54:00:b4:0d:b5  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlo1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.42.0.2  netmask 255.255.255.0  broadcast 10.42.0.255 #<<==================== 10.42.0.2
        inet6 fe80::4889:3a82:ec42:53b9  prefixlen 64  scopeid 0x20<link>
        ether c8:09:a8:89:c3:4e  txqueuelen 1000  (Ethernet)
        RX packets 83076  bytes 82626897 (82.6 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 50036  bytes 42741093 (42.7 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

wlxb0386cf34c0b: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.137  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::f6a1:349f:54cf:2a66  prefixlen 64  scopeid 0x20<link>
        ether b0:38:6c:f3:4c:0b  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

현재 할당받은 IP주소가 `10.42.0.2`인 것을 확인했다. 이제 `nmap`을 실행하여 현재 네트워크에 `10.42.0.`으로 시작하는 주소가 할당된 단말을 찾아보자.

```bash
nmap -sn 10.42.0.0/24
Starting Nmap 7.80 ( https://nmap.org ) at 2026-07-15 12:04 KST
Nmap scan report for S10e_Hotspot (10.42.0.1))# <<====================== 휴대폰 핫스팟
Host is up (0.084s latency).
Nmap scan report for a10sc (10.42.0.2)# <<============================== PC 노트북
Host is up (0.00038s latency).
Nmap done: 256 IP addresses (2 hosts up) scanned in 3.17 seconds # <==== 총 2개의 단말 발견
```

위 결과로 휴대폰 핫스팟 연결 시 휴대전화, 노트북 PC에 할당된 주소를 알아낼 수 있었다. 이제 Limo로봇을 휴대폰 핫스팟에 연결 후 `nmap`을 실행해보자.

```bash
nmap -sn 1.42.0.0/24
```



```bash
nmap -sn 10.42.0.0/24
Starting Nmap 7.80 ( https://nmap.org ) at 2026-07-15 12:04 KST
Nmap scan report for S10e_Hotspot (10.42.0.1))# <<====================== 휴대폰 핫스팟
Host is up (0.084s latency).
Nmap scan report for a10sc (10.42.0.2)# <<============================== PC 노트북
Host is up (0.00038s latency).
Nmap scan report for a10sc (10.42.0.3)# <<============================== 새로발견된 단말(Limo 로봇)
Nmap done: 256 IP addresses (3 hosts up) scanned in 3.18 seconds # <==== 총 3개의 단말 발견
```

Limo 로봇에 `openssh-server`가 설치되었고, IP주소가 `10.42.0.3`인 것을 알아냈으므로 이제 Limo로봇에 연결할 준비가 완료되었다.

터미널에서 다음 명령으로 Limo로봇에 `ssh`원격 접속한다.

```
ssh wego@10.42.0.3
```

```
ssh wego@10.42.0.3
wego@10.42.0.3's password:
```

이 때 패스워드는 `wego`를 입력한다.

```
ssh wego@10.42.0.3
wego@10.42.0.3's password: 
Welcome to Ubuntu 18.04.6 LTS (GNU/Linux 4.9.253-tegra aarch64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage
This system has been minimized by removing packages and content that are
not required on a system that users do not log into.

To restore this content, you can run the 'unminimize' command.

554 updates can be applied immediately.
306 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable

*** System restart required ***
Last login: Wed Jul 15 09:31:50 2026 from 10.42.0.2
wego@wego-robotics:~$_
```



#### 4. `ROS_MASTER_URI`, `ROS_HOSTNAME` 설정

##### 4.1 Limo 로봇

`ssh`원격 접속화면에서 `~/.bashrc`파일을 편집한다.

```bash
nano ~/.bashrc
```

`.bashrc`파일 내용 중 `source /opt/ros/melodic/setup.bash`를 찾아 그 다음 행에 다음 내용을 추가한다.

```bash
export ROS_MASTER_URI=http:[노트북PC의 IP주소]:11311
export ROS_HOSTNAME=[Limo로봇의 IP주소]
```

`ROS_MASTER_URI`는 `roscore`가 실행될 시스템의 프로토콜 및, 포트번호를 포함한 IP주소이다. 이 후 실습에서 `roscore`는 노트북 PC에서 구동할 것이므로 이 경우 `http://10.42.0.2:11311`이 되겠다.

`ROS_HOSTNAME`은 프로토콜 및 포트번호를 제외한 ROS가 운영되는 시스템 자신의 IP주소이므로 이 경우 로봇 자신의 IP주소인  `10.42.0.3`이다.

편집이 완료되면 `nano`편집기 종료를 위해 `Ctrl-X`를, 변경내용 저장여부 확인에 저장을 위해`y`를, 파일명`bashrc`으로 저장 확인에`Enter를`순서대로 입력하여 편집내용을 저장 후 종료한다. 

`ssh`원격 접속화면에서`source`명령으로 변경된  `~/.bashrc` 반영하기 위해 다음 명령을 실행한다.

```bash
source ~/.bashrc
```



##### 4.2 PC 

새 터미널을 열고 `~.bashrc` 편집을 위해 다음 명령을 실행한다.

```bash
gedit ~/.bashrc
```

`.bashrc`파일 내용 중 `source /opt/ros/noetic/setup.bash`를 찾아 그 다음 행에 다음 내용을 추가한다.

```bash
export ROS_MASTER_URI=http:[노트북PC의 IP주소]:11311
export ROS_HOSTNAME=[노트북PC의 IP주소]
```

`ROS_MASTER_URI`는 `roscore`가 실행될 시스템의 프로토콜 및, 포트번호를 포함한 IP주소이다. 이 후 실습에서 `roscore`는 노트북 PC에서 구동할 것이므로 이 경우 `http://10.42.0.2:11311`이 되겠다.

`ROS_HOSTNAME`은 프로토콜 및 포트번호를 제외한 ROS가 운영되는 시스템 자신의 IP주소이므로 노트북 PC의 IP주소인  `10.42.0.2`이다.

편집한 내용을 저장 후 편집기를 종료하고 현재 터미널 창에 수정된 `.bashrc`반영을 위해 다음 명령을 실행한다.

```bash
source ~/.bashrc
```



#### 5. Limo 로봇 구동 테스트

##### 5.1 노트북 PC에서 `roscore` 실행

노트북 PC에서 새 터미널을 열고 `roscore`를 구동한다.

```bash
roscore
```



##### 5.2 Limo 로봇에서 `limo_bringup` 패키지의 `limo_start.launch` 구동

새 터미널에서` ssh wego@[로봇IP주소]` 명령으로 Limo 로봇에 `ssh`원격 연결한다.

`ssh`원격 연결 창에서 다음 명령을 실행하여 Limo로봇을 구동한다.

```bash
 roslaunch limo_bringup limo_start.launch
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



##### 5.3 노트북 PC에서 Limo 로봇 구동 토픽 `/cmd_vel` 발행



노트북 PC에서 `rostopic pub `명령으로 Limo 로봇을 움직여보자. 새 터미널을 열고 다음 명령을 실행한다. 

```bash
rostopic pub -r 10 /cmd_vel geometry_msgs/Twist \
'{linear: {x: 0.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.125}}'
```

Limo로봇이 제자리에서 왼쪽으로 천천히 회전한다. 로봇을 정지 시키려면 다음 명령을 실행한다.

```bash
rostopic pub -1 /cmd_vel geometry_msgs/Twist \
'{linear: {x: 0.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}'
```





[튜토리얼 목록](../README.md) 





