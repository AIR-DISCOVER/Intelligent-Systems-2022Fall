# 仿真EP配置

在运行本节指南前，请先[启动仿真环境](HOW-TO-SIM.md#2)并[将相机输出可视化](HOW-TO-SIM.md#3)。

## 拉取镜像

课程提供性能欠佳但功能正常的EP客户端镜像。通过以下命令拉取：

```shell
docker pull docker.discover-lab.com:55555/rmus-2022-fall/client-cpu
```

## 启动仿真EP

推荐先按照 2.1 节说明将仿真环境中的相机可视化，便于观察小车移动。

通过以下命令在后台启动客户端：

```shell
docker run -dit --rm --network net-sim --name client \
	--cpus=5.6 -m 8192M \
	-v /dev:/dev -e DISPLAY=$DISPLAY -e QT_X11_NO_MITSHM=1 \
    -e ROS_MASTER_URI=http://ros-master:11311 \
	-v /dev/bus/usb:/dev/bus/usb \
    -v /dev/video0:/dev/video0 \
    -v /dev/video1:/dev/video1 \
    -v /dev/video2:/dev/video2 \
    -v /dev/video3:/dev/video3 \
    -v /dev/video4:/dev/video4 \
    -v /dev/video5:/dev/video5 \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    docker.discover-lab.com:55555/rmus-2022-fall/client-cpu bash
```

## 使用键盘控制小车移动

客户端启动后，执行以下命令启动键盘监听：

```shell
docker exec -it client /opt/ros/noetic/env.sh /opt/ep_ws/devel/env.sh roslaunch ep_teleop keyboard.launch
```

* 按 `i` ，`j` ，`,` ，`l` 分别控制小车前进、后退、旋转
* 按 `I` ，`J` ，`<` ，`L` 控制小车水平方向移动
* 按 `k` 停止小车移动
* 按 `1` 放下小车机械臂
* 按 `2` 抬起小车机械臂
* 按 `3` 关闭抓爪
* 按 `4` 打开抓爪
* 按 `Ctrl + C` （Windows）/ `Control + C` （Mac）停止监听
