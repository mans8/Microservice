



三大指标：高可用、高性能、高并发

高性能

- RPC通信
- Kyro高速序列化
- HikariCP连接池
- SQL优化
- Redis缓存
- JVM优化
- GC优化

高并发

- 垂直扩展
- 水平扩展
- 负载均衡
- 集群

高可用（一直可以用，99.999%）

- 解决单点故障问题，实现崩溃恢复
- 自动扩缩容
- Base理论
- Kubernetes一定简历在容器引擎之上
- 服务要更新，金丝雀发布，滚动更新，版本回滚



## 1. Kubernetes简介

​		一套容器编排系统（是什么），**最重要是解决高可用**（做什么），具备以下几个功能

1. 容器自动重启
2. 容器自动扩缩容
3. 能实现滚动更新，金丝雀发布

docker无法做到自动扩缩容，docker是测试级别，Kubernetes是生产级别。

### 1.2 运用

- 快速部署应用
- 快速扩展应用
- 无缝对接新的应用功能
- 节省资源，优化硬件资源应用

### 1.3 特点

- 可移植：支持公有云、私有云、混合云、多重云（多个公共云）
- 可扩展：模块化，插件化，可挂载，可组合
- 自动化：自动部署，自动重启，自动复制，自动伸缩扩展

### 1.4 为什么需要Kubernetes

可以在物理和虚拟机的Kubernetes集群上运行容器化应用，Kubernetes能提供一个以“**容器为中心的基础架构**”，满足生产环境中运行应用的一些常见需求，如：

1. 多个进程协同工作
2. 存储系统挂载
3. 应用健康检查
4. 应用实例的复制
5. 自动伸缩/扩展
6. 注册与发现
7. 负载均衡
8. 滚动更新
9. 资源监控
10. 日志访问
11. 调试应用程序
12. 提供认证和授权



## 2. 安装

### 2.1 节点配置

1主2从

| 主机名             | IP           | 角色  | 系统                | CPU/内存 | 磁盘 |
| ------------------ | ------------ | ----- | ------------------- | -------- | ---- |
| Kubernetes-master  | 192.168.1.60 | Maser | Ubuntu Server 18.04 | 2核4G    | 20G  |
| Kubernetes-node-01 | 192.168.1.61 | Node  | Ubuntu Server 18.04 | 2核4G    | 20G  |
| Kubernetes-node-02 | 192.168.1.62 | Node  | Ubuntu Server 18.04 | 2核4G    | 20G  |

### 2.2 统一环境配置和安装docker环境

在制作镜像时一并完成避免逐台安装的的痛苦

```shell
#关闭交换空间（虚拟化技术必须关闭交换空间，避免造成资源浪费，阿里云上也没有交换空间）
swapoff -a
-------------------------------------------------------------------------------------------
#查看交换空间
free -h
-------------------------------------------------------------------------------------------
#避免开机启动交换空间，注释swap开头的行
vi /etc/fstab
-------------------------------------------------------------------------------------------
#关闭防火墙
ufw disable
-------------------------------------------------------------------------------------------
#配置DNS
#取消DNS行注释，并增加DNS配置如：114.114.114.114，修改后重启计算机
vi /etc/systemd/resolved.conf
-------------------------------------------------------------------------------------------
#安装Docker
#更新数据源
sudo apt-get update
#安装所需依赖
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
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
-------------------------------------------------------------------------------------------
#安装Kubernetes必备工具（kubeadm、kubelet、kubectl）
#安装系统工具
apt-get update && apt-get install -y apt-transport-https
#安装GPG证书
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -
#写入软件源，注意：我们用系统代号为bionic，但目前阿里云不支持，所以沿用16.04的xenial
cat << EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/ apt/kubernetes-xenial main
EOF
#安装
apt-get update && apt-get install -y kubelet kubeadm kubectl
```

### 2.3 同步时区

```shell
#设置同步时区，选择亚洲上海
dpkg-reconfigure tzdata
-------------------------------------------------------------------------------------------
#时间同步
apt-get install ntpdate

#设置系统时间与网络时间同步
ntpdate cn.pool.ntp.org

#将系统时间写入硬件时间
hwclock --systohc

#确认时间
date
```

### 2.4 修改主机名防止还原

```shell
#修改cloud.cfg，主要防止主机名还原
vi /etc/cloud/cloud.cfg
#该配置默认为false，修改为true即可
preserve_hostname: true
#重启
```

### 2.5 单独节点配置

开始克隆节点，并单独为master和node节点单独配置对应的**IP**和**主机名**

```shell
#配置IP
vi /etc/netplan/50-cloud-init.yaml
#修改内容（:set paste粘贴）
network:
  version: 2
  ethernets:
    ens33:
      addresses: [192.168.1.60/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [192.168.1.1]

#配置主机名
hostnamectl set-hostname kubernetes-master

#修改host文件（加入192.168.1.60 kubernetes-master）
vi /etc/hosts
```







