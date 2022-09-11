# 推送镜像和拉取镜像

> **基础概念**
>
> 区别于官方的Docker Registry，课程搭建平台中的镜像分为以下三个层次：
>
> * `PROJECT`: 项目，多个不同用途的仓库的集合。在本课程中，每一位同学只有一个名为`学号-project`的项目；此外，公共镜像所在的项目为`rmus-2022-fall`
> * `REPO`：仓库，对应一个特定用途的容器
> * `TAG`：标签，一个仓库中容器的不同版本。没有特别指定时，默认的标签为`latest`
>
> 例子：`docker.discover-lab.com:55555/rmus-2022-fall/sim-headless:v3.0.2`
>
> * `PROJECT`: `rmus-2022-fall`
> * `REPO`: `sim-headless`
> * `TAG`: `v3.0.2`

## 登录至平台

将个人的镜像推送到平台需要验证身份。在实验运行的命令行环境中运行下列命令以登录：

```shell
docker login docker.discover-lab.com:55555
```

登录用户名/密码与门户网站用户名/密码一致（用户名为**学号**，初始密码为**`学号ABCdef`**）。

## 从服务器端拉取镜像到本地

```shell
docker pull docker.discover-lab.com:55555/PROJECT/REPO[:TAG]
```

其中，`PROJECT`、`REPO`、`TAG`是被拉取的镜像的项目、仓库、标签。

## 推送本地镜像到服务器

只有命名形如`docker.discover-lab.com:55555/PROJECT/REPO[:TAG]`的镜像才能被正确推送到课程镜像平台。因此，首先将本地的容器重新命名：

```shell
docker tag SOURCE_IMAGE[:TAG] docker.discover-lab.com:55555/2018012048-project/REPOSITORY[:TAG]
```

其中`SOURCE_IMAGE[:TAG]`为本地镜像名及其标签。

然后运行以下命令开始推送镜像：

```shell
docker push docker.discover-lab.com:55555/2018012048-project/REPOSITORY[:TAG]
```

