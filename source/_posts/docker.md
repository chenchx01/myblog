---
title: docker
author: Chenchx
top: false
cover: false
toc: true
mathjax: false
date: 2021-07-22 19:47:40
img:
coverImg:
password:
summary: Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。 Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。
tags: docker
categories:
---
# Docker

Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

## 一、安装Docker
  
```shell 

1.安装依赖包
sudo yum install -y yum-utils device-mapper-persistent-data lvm2

2.设置阿里云镜像源
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

3.安装 docker-ce
sudo yum install docker-ce

4.启动 docker-ce
sudo systemctl enable docker

sudo systemctl start docker

5.镜像加速配置
这里使用的是 阿里云提供的镜像加速 ，登录并且设置密码之后在左侧的 Docker Hub 镜像站点 可以找到专属加速器地址，复制下来。
 
在路径/etc/docker  添加daemon.json文件，内容为

{

  "registry-mirrors": ["https://jyrysgjm.mirror.aliyuncs.com"]

}

创建并修改完daemon.json文件后，需要让这个文件生效
  
6.修改完成后reload配置文件

sudo systemctl daemon-reload

7.重启docker服务

sudo systemctl restart docker.service

8.查看状态

sudo systemctl status docker -l

9.查看服务

sudo docker info

```


## 二、Docker 常用命令
### 2.1.拉取镜像
```shell 
docker pull xxx
```

### 2.2.删除容器
```shell 
docker rm <容器名 or ID>
```

### 2.3.查看容器日志
```shell 
docker logs -f <容器名 or ID>
```

### 2.4.查看正在运行的容器
```shell 
docker ps
docker ps -a # 为查看所有的容器，包括已经停止的。
```

### 2.5.删除所有容器
```shell 
docker rm $(docker ps -a -q)
```

### 2.6.停止、启动、杀死指定容器
```shell 
docker start <容器名 or ID> # 启动容器
docker stop <容器名 or ID> # 启动容器
docker kill <容器名 or ID> # 杀死容器
```

### 2.7.查看所有镜像
```shell 
docker images
```

### 2.8.拉取、删除镜像
```shell 
docker pull <镜像名:tag># 拉取镜像
docker rmi <容器名 or ID># 删除镜像
# 例如以下代码
docker pull sameersbn/redmine:latest
docker rmi abc123
```

### 2.9.后台运行
```shell 
docker run -d <Other Parameters>
# 例如
docker run -d -p 127.0.0.1:33301:22 centos6-ssh
```

### 2.10.暴露端口
```shell 
# 一共有三种形式进行端口映射
docker -p ip:hostPort:containerPort # 映射指定地址的主机端口到容器端口
# 例如：docker -p 127.0.0.1:3306:3306 映射本机3306端口到容器的3306端口
docker -p ip::containerPort # 映射指定地址的任意可用端口到容器端口
# 例如：docker -p 127.0.0.1::3306 映射本机的随机可用端口到容器3306端口
docer -p hostPort:containerPort # 映射本机的指定端口到容器的指定端口
# 例如：docker -p 3306:3306 # 映射本机的3306端口到容器的3306端口
```

### 2.11.映射数据卷
```shell 
docker -v /home/data:/opt/data # 这里/home/data 指的是宿主机的目录地址，后者则是容器的目录地址
```
### 2.12.docker加载镜像
```shell 
 docker load -i xxx.tar
```

## 三、Dockerfile
```text
| 关键字      | 作用                     | 备注                                                         |
| ----------- | ------------------------ | ------------------------------------------------------------ |
| FROM        | 指定父镜像               | 指定dockerfile基于那个image构建                              |
| MAINTAINER  | 作者信息                 | 用来标明这个dockerfile谁写的                                 |
| LABEL       | 标签                     | 用来标明dockerfile的标签 可以使用Label代替Maintainer 最终都是在docker image基本信息中可以查看 |
| RUN         | 执行命令                 | 执行一段命令 默认是/bin/sh 格式: RUN command 或者 RUN ["command" , "param1","param2"] |
| CMD         | 容器启动命令             | 提供启动容器时候的默认命令 和ENTRYPOINT配合使用.格式 CMD command param1 param2 或者 CMD ["command" , "param1","param2"] |
| ENTRYPOINT  | 入口                     | 一般在制作一些执行就关闭的容器中会使用                       |
| COPY        | 复制文件                 | build的时候复制文件到image中                                 |
| ADD         | 添加文件                 | build的时候添加文件到image中 不仅仅局限于当前build上下文 可以来源于远程服务 |
| ENV         | 环境变量                 | 指定build时候的环境变量 可以在启动的容器的时候 通过-e覆盖 格式ENV name=value |
| ARG         | 构建参数                 | 构建参数 只在构建的时候使用的参数 如果有ENV 那么ENV的相同名字的值始终覆盖arg的参数 |
| VOLUME      | 定义外部可以挂载的数据卷 | 指定build的image那些目录可以启动的时候挂载到文件系统中 启动容器的时候使用 -v 绑定 格式 VOLUME ["目录"] |
| EXPOSE      | 暴露端口                 | 定义容器运行的时候监听的端口 启动容器的使用-p来绑定暴露端口 格式: EXPOSE 8080 或者 EXPOSE 8080/udp |
| WORKDIR     | 工作目录                 | 指定容器内部的工作目录 如果没有创建则自动创建 如果指定/ 使用的是绝对地址 如果不是/开头那么是在上一条workdir的路径的相对路径 |
| USER        | 指定执行用户             | 指定build或者启动的时候 用户 在RUN CMD ENTRYPONT执行的时候的用户 |
| HEALTHCHECK | 健康检查                 | 指定监测当前容器的健康监测的命令 基本上没用 因为很多时候 应用本身有健康监测机制 |
| ONBUILD     | 触发器                   | 当存在ONBUILD关键字的镜像作为基础镜像的时候 当执行FROM完成之后 会执行 ONBUILD的命令 但是不影响当前镜像 用处也不怎么大 |
| STOPSIGNAL  | 发送信号量到宿主机       | 该STOPSIGNAL指令设置将发送到容器的系统调用信号以退出。       |
| SHELL       | 指定执行脚本的shell      | 指定RUN CMD ENTRYPOINT 执行命令的时候 使用的shell            |

```
### 3.1 基础镜像是docker仓库的java:8（JDK8）
```text
FROM java:8

#  作者签名

MAINTAINER Chenchx

#  挂载宿主机jar包到镜像

copy 项目名称.jar /项目名称.jar

#  执行 java -jar 命令，启动容器跟随启动

CMD java -jar  项目名称.jar

#  设置对外端口为 8081

EXPOSE 8081

#docker 容器内的时间不一定与当前时间一直，可能需要指定时区

RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

RUN echo 'Asia/Shanghai' >/etc/timezone

```
### 3.2 构建docker镜像
dockerfile 当前目录下
```text
docker build -t  镜像名称  .
注意最后有个点
```

### 3.3 创建新的容器并运行
```text 
docker run -d --name 容器名称  -p 8888:8080 镜像名称  -v /usr/local/logs:/log/

这里 -d 是后台运行，-p 是指定端口，后面的 8888:8080 是映射宿主机的8888端口到docker的8080端口，这时如果运行成功会打印出一个id

-v  挂载 将日志文件指定到/usr/local/logs下

```

### 3.4 进入容器
```text 
 docker exec -it  容器ID  /bin/bash 
```

### 3.5  容器文件复制
```text  
 sudo docker cp  路径  容器ID：路径    从外部复制文件到容器

 sudo docker cp  容器ID：路径 路径     从容器复制文件到外部 
```

