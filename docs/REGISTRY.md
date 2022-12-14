# 镜像平台与自动评测系统

## 镜像平台

Docker是容器化技术的一种实现。容器形式的应用程序能够从基础设施中分离出来，使得同样的代码能够在不同的平台（Windows，Mac，Ubuntu，Raspberry等）运行。利用Docker的可以快速发布、测试和部署代码，可以显著减少编写代码时适配基础设施的时间开销。

在本次课程中，我们通过Docker镜像的方式提供课程所用仿真环境，该环境包含对比赛场地的模拟；各位同学在实验阶段也需要通过Docker镜像的方式包装设计的算法作为客户端提交。分别通过两个镜像创建的容器，在运行过程中通过ROS Topics的方式交互。

为了便于同学们保存修改后的Docker镜像，课程搭建了Docker镜像平台。平台使用者能将本地的Docker镜像推送到平台上，也可以从平台上将已存在的镜像拉取回本地。作为一个云端设施，镜像平台一方面为同学们的开发过程提供了方便的备份手段，另一方面也能够提升自动化评测的效率。

选课同学的用户已经在平台上创建完成，用户名为**学号**，初始密码为**`学号ABCdef`**；请及时登录镜像平台门户网站修改初始密码。点击访问镜像平台门户网站：[https://docker.discover-lab.com:55555](https://docker.discover-lab.com:55555)

向平台推送镜像、从平台拉取镜像的具体操作步骤请见[这里](SUBMIT-DOCKER.md)。与此同时，我们向各位同学提供了配置好的仿真环境的Docker镜像（[配置指南](how-to/start-sim-env.md)），也提供了一个性能欠佳但能正常运行的客户端Docker镜像（[配置指南](how-to/start-sim-ep.md)），在实验阶段同学们需要基于我们提供的客户端镜像进行修改。

## 评测系统

Docker镜像平台与自动评测系统运行在同一个评测服务器上。当**推送特定命名规范的镜像**时，将触发评测服务器自动创建并启动所提交容器以及仿真环境容器，模拟运行过程进行评价。

