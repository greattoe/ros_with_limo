## How to Start Limo 로봇





---

## ROS 환경에서 Limo로봇 운영

**출처 :**  <http://wiki.ros.org/rospy_tutorials/Tutorials/WritingPublisherSubscriber>

**튜토리얼 레벨 :**  초급

**선수 학습 :**  ROS 튜토리얼

**빌드 환경 :**  catkin **/** Ubuntu 20.04 **/** Noetic

---

#### 0. 준비작업

- 이 후 Limo로봇의 방향기준은 카메라 방향을 전방, 모니터 방향을 후방으로 정한다.

- Limo로봇 충전 어댑터 연결(LIMO로봇의 모니터 아래 후방 덮개를 열어 전원 어댑터를 연결한다.)
- Limo로봇을 위한 키보드 및 마우스 연결(LIMO로봇의 우측면 덮개를 열어 키보드와 마우스를 연결한다.)

- **PC와 Limo로봇의 네트워크 연결 수단** 휴대폰 핫스팟 연결을 준비해 둔다.



#### 1. 전원켜기

좌측 바퀴 사이의 둥근 버튼이 전원 버튼이다. 이 버튼을 3~4초가량 충분히 길게 누르면 '삑'소리와 함께 모니터에 부팅 메세지가 출력되면서 Limo로봇의 젯슨나노 보드가 부팅된다. 부팅이 완료되면 로그인 화면에서 password 입력란에 `wego`를 입력한다.



#### 2. 네트워크 연결

##### 2.1 Limo 로봇

준비된 휴대폰 핫스팟에 연결 후 터미널을 열어 `openssh-server` 설치

```bash
sudo apt install openssh-server
```





##### 2.2 PC

준비된 휴대폰 핫스팟에 연결 후 터미널을 열어 `nmap` 설치

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
nmap -sn 10.42.0.0/24
Starting Nmap 7.80 ( https://nmap.org ) at 2026-07-15 12:04 KST
Nmap scan report for S10e_Hotspot (10.42.0.1))# <<====================== 휴대폰 핫스팟
Host is up (0.084s latency).
Nmap scan report for a10sc (10.42.0.2)# <<============================== PC 노트북
Host is up (0.00038s latency).
Nmap scan report for a10sc (10.42.0.3)# <<============================== 새로발견된 단말(Limo 로봇)
Nmap done: 256 IP addresses (3 hosts up) scanned in 3.18 seconds # <==== 총 3개의 단말 발견
```



#### 





[튜토리얼 목록](../README.md) 







