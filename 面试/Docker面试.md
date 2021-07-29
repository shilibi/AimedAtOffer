## 什么是Docker

Docker是一个容器化平台，它以容器形式将应用程序及其所有依赖项打包在一起，以确保应用程序在任何环境中无缝运行

## Docker与虚拟机有何不同

- 容器不需要引导操作系统内核，因此可以在不到一秒时间内创建容器，
- 基于容器的虚拟化具有接近本机的性能
- 对基于容器的虚拟化，与其他虚拟化不同，不需要其他软件
- 主机上所有容器共享主机的调度程序，节省额外资源的需求
- 容器很小，因此容器镜像很容易分发

## 什么是Docker镜像

Docker镜像是Docker容器的源代码，Docker镜像用于创建容器

## 什么是Docker容器

Docker容器包括应用程序及其所有依赖项，作为操作系统的独立进程运行

## Docker四种状态

运行、已暂停、重新启动、已退出

## Dockerfile最常见的指令是什么

FROM：指定基础镜像

LABEL：功能是为镜像指定标签

RUN：运行指定的命令

CMD：容器启动时要运行的命令

## Dockerfile命令COPY和ADD区别？

COPY的SRC只能是本地文件

## 容器与主机之间的数据拷贝命令

docker cp 用于容器和主机之间的数据拷贝

## Dockerfile的onbuild指令

当镜像用作另一个镜像的基础时，onbuild指令想镜像添加将在稍后执行的触发指令。

## Docker Swarm

是Docker的本地群集，将Docker主机池转变为单个虚拟Docker主机

## 监控Docker

Docker 提供docker stat 和docker事件等工具监控

