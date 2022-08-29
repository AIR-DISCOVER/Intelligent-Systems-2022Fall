# 仿真环境操作指南

[toc]

## 1. 仿真环境准备

### 1.1 安装Docker环境

Docker是容器化技术的一种实现。容器形式的应用程序能够从基础设施中分离出来，使得同样的代码能够在不同的平台（Windows，Mac，Ubuntu，Raspberry等）运行。利用Docker的可以快速发布、测试和部署代码，可以显著减少编写代码时适配基础设施的时间开销。

在本次比赛中，我们通过Docker容器的方式向参赛者提供比赛所用的仿真环境，该环境包含对比赛场地的模拟，参赛者的容器通过ROS Topics的方式与仿真环境交互。仿真测评阶段，将由评测服务器自动创建仿真环境容器与参赛者提交容器，并自动启动进行模拟过程进行评价。

#### 1.1.1 安装Docker环境 - Windows系统

==TODO==

Docker Desktop

#### 1.1.2 安装Docker环境 - Ubuntu 系统

创建文件 `install.sh`：

```shell
#!/bin/bash
set -x 
set -e

apt-get update

apt-get -y install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update
apt-get install -y docker-ce docker-ce-cli containerd.io
```

执行以下命令：

```shell
chmod +x install.sh
sudo ./install.sh
sudo usermod -aG docker $USER
```

执行完成后，注销后重新登录即可使用 Docker 容器。执行以下命令检验安装：

```shell
docker ps
```

正确的运行结果应为

```
CONTAINER ID    IMAGE    COMMAND    CREATED    STATUS    PORTS
```

### 1.2 拉取镜像

比赛提供的仿真环境容器是公开的。参赛者可以通过以下命令将该容器从云端拉取到本地：

```shell
docker pull docker.discover-lab.com:55555/rmus-2022-fall/sim-headless:v3.0.1
```

容器大小为11.23GB，拉取时间较长，请耐心等待。

此外，启动仿真环境仍需要 `ros:noetic-ros-core-focal` 镜像；对仿真环境进行可视化仍需要 `docker.discover-lab.com:55555/rmus-2022-fall/ros-gui:v3.0.1` 镜像。通过以下命令拉取这两个镜像：

```shell
docker pull ros:noetic-ros-core-focal
docker pull docker.discover-lab.com:55555/rmus-2022-fall/sim-headless:v3.0.1
```

### 1.3 启动仿真环境

#### 1.3.1 创建虚拟网卡

```shell
docker network create net-sim
```

#### 1.3.2 启动通讯节点

```shell
docker run -dit --rm --name ros-master --network net-sim ros:noetic-ros-core roscore
```

#### 1.3.3 启动仿真环境

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

## 2. 仿真环境测试

### 2.1 相机输出可视化

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
    ros-gui bash
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

### 2.2 环境交互

==TODO==

## 3. 停止仿真环境

停止创建的容器即可。注意，此处容器在停止后将会立即删除，任何对容器的修改将不会被保存。

```shell
docker stop sim-server
docker stop ros-gui
docker stop ros-master
docker network rm net-sim
```

如需再次启动仿真环境，按照 `1.3` 节说明操作即可。