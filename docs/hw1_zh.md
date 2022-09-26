# Intelligent Systems HW1

**在下列教程中，请将 `[Student ID]` 替换为自己的学号。**

## 这次作业的目标是什么？

![reverse-kinematics](assets/reverse-kinematics.png)

本次作业中，需要根据机械臂末端夹爪的位姿，解出机械臂的两个关节角度，也就是实现机械臂的逆运动学。

此问题数学描述如上图所示，已知连杆长度 $l1$， $l2$，$l3$以及机械臂末端位置$(p_x, p_y)$，求两个关节的角度$\theta_1, \theta_2$

## 我要怎样完成这次作业？

1. 将作业仓库克隆至本地

   ```shell
   git clone https://github.com/AIR-DISCOVER/IS2022Fall-hw1.git
   ```

2. 补充 `course_ws/src/me_arm/script/reverse.py` 文件中的实现。

   标注有 `[TODO]`的部分是需要你实现的。 

3. 在 `IS2022Fall-hw1` 目录下，执行以下命令将项目编译为Docker镜像。

   ```shell
   docker build . -t docker.discover-lab.com:55555/[Student ID]/client:hw1 
   ```

## 如何检验我的实现是否正确？

> 首先启动仿真环境，并将相机可视化

1. 启动仿真容器：

   注意，在本次作业中，仿真环境的 tag 为 **hw1**。

   在**新的终端**中执行以下命令启动仿真环境：

   ```shell
   docker network create net-sim
   docker run -dit --rm --name ros-master --network net-sim ros:noetic-ros-core-focal roscore
   docker run -it --rm --name sim-server --network net-sim -e ROS_MASTER_URI="http://ros-master:11311" --gpus all docker.discover-lab.com:55555/rmus-2022-fall/sim-headless:hw1
   ```

2. 可视化相机输出：

   在**新的终端**执行以下命令启动可视化：

   ```shell
   xhost +
   docker run -dit --rm --name ros-gui --network net-sim -e ROS_MASTER_URI=http://ros-master:11311 -e DISPLAY=$DISPLAY -e QT_X11_NO_MITSHM=1 -v /tmp/.X11-unix:/tmp/.X11-unix docker.discover-lab.com:55555/rmus-2022-fall/ros-gui bash
   docker exec -dit ros-gui /opt/ros/noetic/env.sh rosrun image_view image_view image:=/third_rgb
   ```

3. 根据[上一节](#2)编译得到的镜像创建<u>控制容器</u>并执行：

   在**新的终端**执行以下命令启动作业容器：

   ```shell
   docker run -it --rm -e ROS_MASTER_URI="http://ros-master:11311" --network net-sim --name hw1 docker.discover-lab.com:55555/[Student ID]/client:hw1
   ```

> 检查实现的正确性

4. 下面的命令会初始化场景，随机生成1/2/3层木块（最顶层木块一定为木块5），并将小车放在距离木块较近的***<u>随机位置</u>***。

   ```shell
   docker exec -it ros-master /opt/ros/noetic/env.sh rostopic pub /reset geometry_msgs/Point "x: 0.0
   y: 0.0
   z: 0.0"
   ```

   正确实现的控制容器，无论小车和木块的相对位置如何，每一次随机初始化后小车的夹爪都会放在**木块5所在的位置**。


> 实验收尾

5. 停止运行中的容器

   ```shell
   docker network rm net-sim
   docker stop hw1
   docker stop sim-server
   docker stop ros-master
   ```

## 如何提交我的作业？

如果你确认你的实现正确，你可以通过以下命令将你的控制容器镜像推送到课程Docker平台。

我们的评测系统会自动运行你的容器并打分。    

```shell
docker login docker.discover-lab.com:55555
# Input your Student ID and password
# The default password is [Student ID]ABCdef
# You can change it later in https://docker.discover-lab.com:55555
docker push docker.discover-lab.com:55555/[Student ID]/client:hw1
```