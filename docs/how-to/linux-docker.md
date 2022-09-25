# Linux系统下准备环境

本节描述如何在Ubuntu系统下安装Docker，如果使用其他Linux发行版，请联系助教获取帮助。

## 安装Docker

打开命令行，在命令行输入`gedit`打开文本编辑器，复制并粘贴以下内容：

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

systemctl restart docker
```

点击“Save”或“保存”，将文件命名为`install.sh`。然后执行以下命令：

```shell
sudo bash install.sh
sudo usermod -aG docker $USER
```

执行完成后，重启电脑（虚拟机）即可使用 Docker 容器。执行以下命令检验已经成功安装：

```shell
docker ps
```

正确的运行结果应为

```
CONTAINER ID    IMAGE    COMMAND    CREATED    STATUS    PORTS
```

## 安装Docker的GPU扩展

如果你的电脑**包含NVIDIA显卡**，可以执行以下命令安装Docker的GPU扩展：

```shell
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
            sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
            sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list

sudo apt-get update
sudo apt-get install -y nvidia-docker2
sudo systemctl restart docker
```

安装之后，在后续步骤中可以使用GPU版本的仿真环境镜像并大幅提高仿真速度。
