# 仿真环境配置

本节提供配置仿真环境并进行测试的说明。

## 拉取镜像

比赛提供的仿真环境容器是公开的。参赛者可以通过以下命令将该容器从云端拉取到本地：

```shell
docker pull docker.discover-lab.com:55555/rmus-2022-fall/sim-headless:v3.0.1
```

容器大小为11.23GB，拉取时间较长（良好网络环境下需20分钟），请耐心等待。

此外：

* 启动仿真环境需要 `ros:noetic-ros-core-focal` 镜像
* 对仿真环境进行可视化需要 `docker.discover-lab.com:55555/rmus-2022-fall/ros-gui:v3.0.1` 镜像
* 控制小车与仿真环境需要 `docker.discover-lab.com:55555/rmus-2022-fall/client:v3.0.1rc` 镜像

通过以下命令分别拉取这三个镜像：

```shell
docker pull ros:noetic-ros-core-focal
docker pull docker.discover-lab.com:55555/rmus-2022-fall/ros-gui:v3.0.1
docker pull docker.discover-lab.com:55555/rmus-2022-fall/client:v3.0.1rc
```

## 启动仿真环境

### 创建虚拟网卡

```shell
docker network create net-sim
```

### 启动通讯节点

```shell
docker run -dit --rm --name ros-master --network net-sim ros:noetic-ros-core roscore
```

### 启动仿真环境

```shell
docker run -it --rm --gpus all \
	--name sim_server \
    --network net-sim \
	-e ROS_MASTER_URI=http://ros-master:11311 \
	-e MAGNUM_LOG=verbose \
	-e MAGNUM_GPU_VALIDATION=on \
	-v /dev/bus/usb:/dev/bus/usb \
    -v /dev/video0:/dev/video0 \
    -v /dev/video1:/dev/video1 \
    -v /dev/video2:/dev/video2 \
    -v /dev/video3:/dev/video3 \
    -v /dev/video4:/dev/video4 \
    -v /dev/video5:/dev/video5 \
	docker.discover-lab.com:55555/rmus-2022-fall/sim-headless:v3.0.1
```

## 仿真环境测试

### 相机输出可视化

执行以下命令在后台启动测试容器：

```shell
xhost +
docker run -dit --rm \
    --name ros-gui \
    --network net-sim \
    -e ROS_MASTER_URI=http://ros-master:11311 \
    -e DISPLAY=$DISPLAY \
    -e QT_X11_NO_MITSHM=1 \
	-v /dev/bus/usb:/dev/bus/usb \
    -v /dev/video0:/dev/video0 \
    -v /dev/video1:/dev/video1 \
    -v /dev/video2:/dev/video2 \
    -v /dev/video3:/dev/video3 \
    -v /dev/video4:/dev/video4 \
    -v /dev/video5:/dev/video5 \
    -v /tmp:/tmp \
    docker pull docker.discover-lab.com:55555/rmus-2022-fall/ros-gui:v3.0.1 bash
```

测试容器启动后，执行以下命令进行可视化：

```shell
# 第三人称视角小车全貌
docker exec -dit ros-gui bash -c \
"source /opt/ros/noetic/setup.bash; rosrun image_view image_view image:=/third_rgb"
# 小车RGB相机输出
docker exec -dit ros-gui bash -c \
"source /opt/ros/noetic/setup.bash; rosrun image_view image_view image:=/camera/color/image_raw"
# 小车深度相机输出
docker exec -dit ros-gui bash -c \
"source /opt/ros/noetic/setup.bash; rosrun image_view image_view image:=/camera/aligned_depth_to_color/image_raw"
```

### 使用键盘控制小车移动

推荐先按照 2.1 节说明将仿真环境中的相机可视化，便于观察小车移动。

通过以下命令在后台启动客户端：

```shell
docker run -dit --rm --gpus all --network net-sim --name client \
	--cpus=5.6 -m 8192M \
	-v /dev:/dev -e DISPLAY=:2 -e QT_X11_NO_MITSHM=1 \
    -e ROS_MASTER_URI=http://ros-master:11311 \
	-v /dev/bus/usb:/dev/bus/usb \
    -v /dev/video0:/dev/video0 \
    -v /dev/video1:/dev/video1 \
    -v /dev/video2:/dev/video2 \
    -v /dev/video3:/dev/video3 \
    -v /dev/video4:/dev/video4 \
    -v /dev/video5:/dev/video5 \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    docker.discover-lab.com:55555/rmus-2022-fall/client:v3.0.1rc $@
```

客户端启动后，执行以下命令启动键盘监听：

```shell
docker exec -dit ros-gui bash -c \
"source /opt/ros/noetic/setup.bash; roslaunch ep_teleop keyboard.launch"
```

* 按 `i` ，`j` ，`,` ，`l` 分别控制小车前进、后退、旋转
* 按 `I` ，`J` ，`<` ，`L` 控制小车水平方向移动
* 按 `k` 停止小车移动
* 按 `1` 抬起小车机械臂
* 按 `2` 放下小车机械臂
* 按 `3` 关闭抓爪
* 按 `4` 打开抓爪
* 按 `Ctrl + C` （Windows）/ `Control + C` （Mac）停止监听

## 停止仿真环境

停止创建的容器即可。注意，此处容器在停止后将会立即删除，任何对容器的修改将不会被保存。

```shell
docker stop sim-server
docker stop ros-gui
docker stop ros-master
docker stop client
docker network rm net-sim
```

如需再次启动仿真环境，按照 `1.3` 节说明操作即可。