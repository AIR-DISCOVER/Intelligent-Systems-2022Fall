# 推送镜像和拉取镜像

> **基础概念**
>
> 区别于官方的Docker Registry，课程搭建平台中的镜像分为以下三个层次：
>
> * `PROJECT`: 项目，多个不同用途的仓库的集合。在本课程中，每一位同学只有一个名为`学号-project`的项目
> * `REPO`：仓库，一个特定用途的容器
> * `TAG`：标签，一个仓库中容器

## 登录至平台

在实验运行的命令行环境中运行下列命令以登录：

```shell
docker login docker.discover-lab.com:55555
```

登录用户名/密码与门户网站用户名/密码一致（用户名为**学号**，初始密码为**`学号ABCdef`**）。

## 从服务器端拉取镜像到本地

```shell
docker pull docker.discover-lab.com:55555/PROJECT/REPO[:TAG]
```



## 推送本地镜像到服务器

```shell
docker tag SOURCE_IMAGE[:TAG] docker.discover-lab.com:55555/2018012048-project/REPOSITORY[:TAG]
docker push docker.discover-lab.com:55555/2018012048-project/REPOSITORY[:TAG]
```

