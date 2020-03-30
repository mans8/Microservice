## 1.docker简介

​		Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中,然后发布到任何流行的[Linux](https://baike.baidu.com/item/Linux)机器或Windows 机器上,也可以实现虚拟化,容器是完全使用沙箱机制,相互之间不会有任何接口。

**一个完整的Docker有以下几个部分组成：**

1. DockerClient客户端
2. Docker Daemon守护进程
3. Docker Image镜像
4. DockerContainer容器



**为什么需要docker**

1. 1.更高效地利用系统资源。与宿主机共享硬件资源
2. 2.更快速的启动时间。docker容器应用直接运行与宿主机内核，无需启动完整的操作系统。
3. 3.一致的运行环境。docker镜像提供除内核外完整的运行时环境，确保应用运行环境一致性。
4. 4.持续交付和部署。
5. 5.更轻松的迁移。
6. 6.更轻松的维护和扩展。

**Docker对比传统虚拟机**

| 特性       | 容器               | 虚拟机     |
| ---------- | ------------------ | ---------- |
| 启动       | 秒级               | 分钟级     |
| 硬盘使用   | 一般为MB           | 一般为GB   |
| 性能       | 接近原生           | 弱于       |
| 系统支持量 | 单机支持上千个容器 | 一般几十个 |

JVM是一次编译到处运行，docker是一次构建到处运行。



## 2.安装Docker

### 2.1Docker CE和EE

Docker 从 17.03版本之后分为 CE（Community Edition） 和 EE（Enterprise Edition）。

**Docker EE**
		Docker EE由公司支持，可在经过认证的操作系统和云提供商中使用，并可运行来自Docker Store的、经过认证的容器和插件。Docker EE提供三个服务层次：

- Basic 包含用于认证基础设施的Docker平台，Docker公司的支持，经过 认证的、来自Docker Store的容器与插件；

- Standard 添加高级镜像与容器管理，LDAP/AD用户集成，基于角色的访问控制(Docker Datacenter)；

- Advanced 添加Docker安全扫描，连续漏洞监控；

**Docker CE**
  		Docker CE是免费的Docker产品的新名称，Docker CE包含了完整的Docker平台，非常适合开发人员和运维团队构建容器APP。事实上，Docker CE 17.03，可理解为Docker 1.13.1的Bug修复版本。因此，从Docker 1.13升级到Docker CE 17.03风险相对是较小的。



### 2.2平台支持

**桌面**

| 平台                                             | 架构 |
| ------------------------------------------------ | ---- |
| Docker Desktop for Mac(macOS)                    | X64  |
| Docker Desktop for Windows(Microsoft Windows 10) | X64  |

**服务器**

| 平台   | x86_64/amd64 | ARM  | ARM64/AARCH64 | IBM Power(ppc64le) | IBM Z(s390x) |
| ------ | ------------ | ---- | ------------- | ------------------ | ------------ |
| CentOS | ✔            |      | ✔             |                    |              |
| Debian | ✔            | ✔    | ✔             |                    |              |
| Fedora | ✔            |      | ✔             |                    |              |
| Ubuntu | ✔            | ✔    | ✔             | ✔                  | ✔            |

### 2.3安装docker和修改数据源

```shell
#卸载旧版本
apt-get remove docker docker-engine docker.io containerd runc

#使用APT安装
#更新数据源
apt-get update
#安装所需依赖
sudo apt install apt-transport-https ca-certificates curl software-properties-common
#安装GPG证书(验证软件是否被篡改)
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
#新增阿里云docker数据源,Ubuntu的$(lsb_release -cs)是bionic
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
#更新apt源并安装docker
sudo apt-get update && sudo apt install -y docker-ce

#验证安装是否成功
docker version

#配置docker的阿里云镜像，登录阿里云找镜像加速器
#可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://sqymagi8.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

#安装一个tomcat
docker pull tomcat
docker images
docker run -p 8080:8080 tomcat

```

使用Docker的运行tomcat不需要安装java，以上是全部步骤。

## 3.Docker概述

### 3.1Docker引擎



|        | 分层     | 作用                                                         |
| ------ | -------- | ------------------------------------------------------------ |
| 上层   | Client层 | 一个有命令行界面的客户端，其上管理着container、network、image、data volumes |
|        | REST API | 由于Docker是C/S架构，Client端与Server端通信的接口            |
| 最底层 | Server   | 一个服务器，有一个守护进程，且长时间运行的程序               |



### 3.2Docker架构

- 镜像image：创建Docker容器的模板；
- 容器container：容器是独立运行的一个或一组应用；
- 客户端client：通过命令行或其它工具使用Docker API与Docker的守护进程通信；
- 主机host：用于执行Docker守护进程和容器；

| Docker | 面向对象 |
| ------ | -------- |
| 镜像   | 类       |
| 容器   | 对象     |

Docker容器通过Docker镜像来创建。容器是镜像的实例，实例不会影响镜像。

**Docker内部交互过程如图：**

![](E:\千锋教育\【千锋达摩院】微服务架构 2.0（上）Linux + Docker + Kubernetes +SpringBoot + SpringCloudAlibaba\Microservice\picture\docker架构.png)



## 4.Docker操作镜像

```shell
#拉取
docker pull $name

#查
docker images  //查看可用的镜像
docker images -a 
docker images ls
docker system df

#删
docker rmi $ID
docker rmi $name

#name为<none>是虚悬镜像或中间态镜像，前者能删，后者不能。
```



## 5.Docker操作容器

```shell
#新增容器（镜像每run一次就新增一个容器）
#左边8080是宿主机端口，右边8080是容器端口，-p 8080:8080是让宿主路由到容器8080，容器和宿主机不是一个局域网
docker run -p 8080:8080 tomcat
docker run -p 8080:8080 -d tomcat
docker run -t -i ubuntu:16.04 /bin/bash
docker run -P tomcat（大写P默认随机分配端口。左边的8080是宿主，右边是容器）
-p：端口映射
-d：守护态运行（后台运行）
-t：进入终端
-i：获得一个交互式的连接，通过获取container的输入
-t -i：以交互的方式运行容器（还有以交互的方式进入容器）
/bin/bash：在container中启动一个bash shell来操作容器

#删除容器
docker rm <container_id>  //删除容器
docker container prune   //删除所有处于终止状态的容器
docker rm $(docker ps -a -q)    //删除所有容器

#查看容器
docker ps  //查看运行中的container
docker ps -l  //查看最新运行的container
docker ps -a  //查看所有运行中、未运行、沉睡的container

#启动容器
docker start <container_id>
docker restart <container_id>
docker run -p 8080:8080 myshop //启动后可以访问了

#停止容器
docker stop <container_id>   //根据镜像id停止
docker stop $(docker ps -a -q)   //停止所有容器

#进入容器
docker attach <container_id> //缺点：从container中退出到前台时，container也跟着退出
docker exec -it <container_id> /bin/bash //优点：当退出container后，container仍然在后台运行。以交互的方式进入容器。

#退出容器
docker退出容器，而不关闭容器：ctrl+q+p
docker退出容器，并且关闭容器：ctrl+c或exit

```

​		容器有隔离机制（沙箱机制），每一个容器损坏都不会影响其它容器和宿主机的运行，离高可用更进一步。Docker使用unionFS技术，相同的层只需要保存一份即可，因此实际镜像硬盘占用空间很可能要比这个列表镜像大小的总和要小得多。





