# 配置Docker运行环境

## 安装Docker环境 - Ubuntu 系统

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

distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
&& curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey |\
gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
&& curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list |\
sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' |\
tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
apt-get install -y nvidia-docker2
systemctl restart docker
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

## 安装Docker环境 - Windows系统