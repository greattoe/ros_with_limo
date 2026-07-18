## Limo 로봇 카메라 영상 수신



카메라 영상 이미지 토픽 퍼블리셔 ROS 패키지를 Limo 로봇에 설치, 구동 후 PC에서 `rqt_image_view`를 이용해 실시간으로 구독해보자.

---

##### 노트북 PC에서 `roscore`실행

새 터미널을 열고 `roscore`구동

```
roscore
```



##### Limo 로봇에서 `usb_cam` ROS 패키지 설치 및 구동

새 터미널 창을 열고 `ssh wego@[Limo로봇 IP주소]`와 같이 Limo로봇에`ssh`원격 연결한다. 

```
ssh wego@10.42.0.3
```

 `usb_cam` ROS 패키지 설치

```
sudo apt install ros-melodic-usb-cam
```

새로 설치된 ROS 패키지 정보 반영

```
source /opt/ros/melodic/setup.bash
```

`usb_cam_node`구동

```
rosrun usb_cam usb_cam_node
```



##### 2. PC에서 `rqt_image_view`  구동

```
rqt_image_view
```

`rqt_image_view`화면에서 `/usb_cam/image_raw`토픽을 선택해 주면 다음과같이 Limo 로봇 카메라 영상을 실시간으로 확인할 수 있다.

![](/home/gnd0/ros_with_limo/doc_md/img/rqt_image_view.png)

이제 원격지에서 실시간으로 로봇 시점에서 로봇을 조종할 수도 있고 ROS `cv_bridge` 패키지와 OpenCV 라이브러리를 이용해 영상처리를 이용한 로봇 제어를 구현할 수도 있다.



[튜토리얼 목록](../README.md) 





