# 获得Docker镜像

这一步需要完成 Docker 安装。如果尚没有完成安装，请依照左侧“准备环境”准备相应环境并安装Docker。

我们提供两种途径获取课程所需要的Docker镜像：**网络拉取**或**文件导入**，请按照其中一种途径完成即可。

完成这一步之后，请参考[启动仿真环境](./start-sim-env.md)和[启动仿真EP](./start-sim-ep.md)测试镜像。

## 通过网络拉取

### 获取通讯节点

在Linux环境下执行以下命令：

```shell
docker pull ros:noetic-ros-core-focal
```

### 获取可视化工具

在Linux环境下执行以下命令：

```shell
docker pull docker.discover-lab.com:55555/rmus-2022-fall/ros-gui
```

### 获取EP小车仿真镜像

在Linux环境下执行以下命令：

```shell
docker pull docker.discover-lab.com:55555/rmus-2022-fall/client-cpu
```

### 获取仿真环境镜像（服务端，二选一即可）：

在Linux环境下执行以下命令，二选一即可：

```shell
# CPU版本
docker pull docker.discover-lab.com:55555/rmus-2022-fall/sim-headless-cpu
```

```shell
# GPU版本，更快更流畅，但需要NVIDIA显卡
docker pull docker.discover-lab.com:55555/rmus-2022-fall/sim-headless
```

运行GPU版本镜像需要**Linux系统**或**Windows系统的WSL**方式准备环境，并完成**安装Docker的GPU扩展**。

## 通过文件导入

如本节中的链接无法打开，请联系助教。

### 获取通讯节点

[点此下载](https://cloud.tianbeiwen.com:8000/f/fb81c031aedf4a3eb725/)（804.5MB）后重命名为 `roscore-focal.tar`，然后导入 Linux 环境的目录下，在该目录下执行：

```shell
docker load < roscore-focal.tar
```

导入后可以执行 `rm roscore-focal.tar` 释放压缩包占用的空间。

### 获取可视化工具

[点此下载](https://cloud.tianbeiwen.com:8000/f/ec9896ff28454320a234/)（2.0GB）后重命名为 `ros-gui.tar`，然后导入 Linux 环境的目录下，在该目录下执行：

```shell
docker load < ros-gui.tar
```

导入后可以执行 `rm ros-gui.tar` 释放压缩包占用的空间。

### 获取EP小车仿真镜像

[点此下载](https://cloud.tianbeiwen.com:8000/f/9d8e4658db034a10b6be/)（6.4GB）后重命名为 `client.tar`，然后导入 Linux 环境的目录下，在该目录下执行：

```shell
docker load < client.tar
```

导入后可以执行 `rm client.tar` 释放压缩包占用的空间。

### 获取仿真环境镜像

[点此下载](https://cloud.tianbeiwen.com:8000/f/8fe64d8dca9143c59dcd/)（15.9GB）CPU版本镜像，或[点此下载](https://cloud.tianbeiwen.com:8000/f/5ff72303ea1046cd9fff/)（24.9GB）GPU版本镜像。

运行GPU版本镜像需要**Linux系统**或**Windows系统的WSL**方式准备环境，并完成**安装Docker的GPU扩展**。

将镜像文件重命名为 `sim-headless.tar`，然后导入 Linux 环境的目录下，在该目录下执行：

```shell
docker load < sim-headless.tar
```

导入后可以执行 `rm sim-headless.tar` 释放压缩包占用的空间。

