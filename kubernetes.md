

Kubernetes中文文档https://kubernetes.io/zh/docs/home/

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



## 1. Kubernetes

### 1.1 简介

一套开源的docker容器编排系统（是什么），调度计算集群节点，动态管理上面的作业，保证它们按照用户期望的状态运行，**最重要是解决高可用**（做什么），具备以下几个功能

1. 容器自动重启
2. 容器自动扩缩容
3. 能实现滚动更新，金丝雀发布

docker无法做到自动扩缩容，docker是测试级别，Kubernetes是生产级别。

![avatar](.\picture\kubernetes.png)

**pods：**一组紧密关联的容器集合，共享IPC（进程间通信）、Network（网络）和UTS namespace（UTS命名空间是Linux命名空间的一个子系统，主要作用是完成对容器Hostname和Domain的隔离，同时保存内核名称、版本，以及底层体系结构类型等信息），是Kubernetes调度的基本单位。

**labels：**键值对（key/value）标签，可以被关联到如pod这样的对象上，主要作用是给用户一个直观的感受，比如这个Pod是用来防止数据库的。

**GUI：**用户图形界面。

**kubectl：**用于管理Kubernetes集群的命令行工具。

**kube-apiserver：**提供了资源操作的唯一入口，提供认证、授权、访问控制、API注册和发现等机制。

**Kubernetes Master：**集群主节点，主要有kube-apiserver、kube-scheduler、kube-controller-manager、etcd四个模块。

> - kube-apiserver：资源操作唯一入口，认证、授权、访问控制、API注册和发现
> - kube-scheduler：资源调度，按照预定的调度策略将Pod调度到响应的机器上
> - kube-controller-manager：负责维护集群状态，比如故障检测、自动扩展、滚动更新等
> - etcd： CoreOS基于Raft开发的分布式key-value存储，可用于服务发现、共享配置以及一致性保障（数据库选主、分布式锁）

**Kubernetes  Node： **kubernetes集群子节点，主要由kubelet、kube-proxy、 runtime三个模块组成。

> - runtime：负责镜像管理以及Pod和容器的真正运行，默认的容器运行时为Docker，还支持RKT容器
> - kubelet：负责维持容器的生命周期，同时也负责Volume（CVI，Container Volume Interface）和网络（CNI，Container Network Interface）的管理
> - kube-proxy：负责为Service提供cluster内部的服务发现和负载均衡

**Image Registry：**镜像仓库，比如：Docker Hub或Docker私服。



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
hostnamectl set-hostname kubernetes-node-02

#修改host文件（加入192.168.1.60 kubernetes-master）
vi /etc/hosts
```

### 2.6 配置及安装k8s

```shell
cd /usr/local
mkdir kubernetes
cd kubernetes
mkdir cluster
kubeadm config print init-defaults --kubeconfig ClusterConfiguration > kubeadm.yml
vi kubeadm.yml
```

```yml
apiVersion: kubeadm.k8s.io/v1beta2
bootstrapTokens:
- groups:
  - system:bootstrappers:kubeadm:default-node-token
  token: abcdef.0123456789abcdef
  ttl: 24h0m0s
  usages:
  - signing
  - authentication
kind: InitConfiguration
localAPIEndpoint:
  #换成master的地址
  advertiseAddress: 192.168.1.60
  bindPort: 6443
nodeRegistration:
  criSocket: /var/run/dockershim.sock
  name: kubernetes-master
  taints:
  - effect: NoSchedule
    key: node-role.kubernetes.io/master
---
apiServer:
  timeoutForControlPlane: 4m0s
apiVersion: kubeadm.k8s.io/v1beta2
certificatesDir: /etc/kubernetes/pki
clusterName: kubernetes
controllerManager: {}
dns:
  type: CoreDNS
etcd:
  local:
    dataDir: /var/lib/etcd
#换成阿里巴巴的景象
imageRepository: registry.aliyuncs.com/google_containers
kind: ClusterConfiguration
kubernetesVersion: v1.18.0
networking:
  dnsDomain: cluster.local
  #pod用来运行容器，可以运行一组容器，运行容器的最小单元
  podSubnet: "10.244.0.0/16"
  serviceSubnet: 10.96.0.0/12
scheduler: {}

```

```shell
#查看下载所需镜像
kubeadm config images list --config kubeadm.yml
------------------------------------------------------------------
#输出如下
#apiserver，网关，提供restful风格的API，类似于docker的守护进程接收解析运行docker命令入口
registry.aliyuncs.com/google_containers/kube-apiserver:v1.18.0
#管理器，自动重启pod
registry.aliyuncs.com/google_containers/kube-controller-manager:v1.18.0
#调度，配置强扛大压，配置弱扛小压
registry.aliyuncs.com/google_containers/kube-scheduler:v1.18.0
#代理，整个k8s是个大型内网，外网到内网的各个节点
registry.aliyuncs.com/google_containers/kube-proxy:v1.18.0
#暂停，跟容器的暂停启动有关系
registry.aliyuncs.com/google_containers/pause:3.2
#服务注册与发现，管理内网中成千上万的服务节点
registry.aliyuncs.com/google_containers/etcd:3.4.3-0
#核心DNS，通过服务名找到服务器，可以跨网段
registry.aliyuncs.com/google_containers/coredns:1.6.7
```

```shell
#拉取所需镜像
kubeadm config images pull --config kubeadm.yml
#输出如下
[config/images] Pulled registry.aliyuncs.com/google_containers/kube-apiserver:v1.18.0
[config/images] Pulled registry.aliyuncs.com/google_containers/kube-controller-manager:v1.18.0
[config/images] Pulled registry.aliyuncs.com/google_containers/kube-scheduler:v1.18.0
[config/images] Pulled registry.aliyuncs.com/google_containers/kube-proxy:v1.18.0
[config/images] Pulled registry.aliyuncs.com/google_containers/pause:3.2
[config/images] Pulled registry.aliyuncs.com/google_containers/etcd:3.4.3-0
[config/images] Pulled registry.aliyuncs.com/google_containers/coredns:1.6.7
```

### 2.7 安装主节点

执行以下命令初始化主节点，该命令指定了初始化时需要使用的配置文件，其中添加`--experimental-upload-certs`参数可以在后续执行加入节点时自动分发证书文件，追加的`tee kubeadm-init.log`用以输出日志。

> **注意：**如果安装kubernetes版本和下载的镜像版本不统一会出现`timed out waiting for the condition`错误。中途失败或是想修改配置可以使用`kubeadm reset`命令重置配置，再做初始化操作即可。

节点间的通信使用的是gRPC，需要有证书，我们服务走的是dubbo。

 

```shell
#V1.15
kubeadm init --config=kubeadm.yml --experimental-upload-certs | tee kubeadm-init.log
#V1.16之后
kubeadm init --config=kubeadm.yml --upload-certs | tee kubeadm-init.log
------------------------------------------------------------------------------
#输出如下
省略........
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:
#配置，在/usr/local/kubernetes/cluster下
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config(管理员root不用这句)
#配置网络
You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.1.60:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:f00f1ccb370ed340e11df145e2ff683d743624e1bb495aeeb5247bb9ad49e6ed 
------------------------------------------------------------------------------
#执行
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
#查看节点
kubectl get node

```

### 2.8 安装从节点

```shell
#加入集群，在log中复制，并在node1和node2的默认/root目录下执行
kubeadm join 192.168.1.60:6443 --token abcdef.0123456789abcdef \
    --discovery-token-ca-cert-hash sha256:f00f1ccb370ed340e11df145e2ff683d743624e1bb495aeeb5247bb9ad49e6ed

#在master机器上执行查看三个节点，因为没有配置网络，所以status是notready
kubectl get node
```

### 2.9 配置网络

容器网络是容器选择连接到其他容器、主机和外部网络的机制。容器的runtime提供了各种网络模式，每种模式都会产生不同的体验。例如，Docker默认情况下可以为容器配置以下网络：

- **none**：将容器添加到一个容器专门的网络堆栈中，没有对外连接
- **host**：将容器添加到主机的网络堆栈中，没有隔离
- **default bridge**：默认网络模式，每个容器可以通过IP地址相互连接。
- **自定义网桥**：用户定义的网桥，具有更多灵活性、隔离性和其它便利功能。

#### 2.9.1 CNI

​		CNI(Container Network Interface)是一个标准的、通用接口。在容器平台，Docker、Kubernetes、Mesos容器网络解决方案flannel，calico，weave。只要提供一个标准接口，就能为同样满足该协议的所有容器平台提供网络功能，CNI就是这样一个标准接口协议。

#### 2.9.2 Kubernetes中的CNI插件

​		CNI点初衷是创建一个框架，用于在配置或销毁容器时动态配置适当的网络配置和资源。插件负责为接口配置和管理IP地址，并且通常提供与IP管理、每个容器的IP分配、以及多主机连接相关的功能。容器运行时会调用网络插件，从而在容器启动时自动分配IP并且配置网络，并在删除容器时再次调用它以清理这些资源。（容器与容器之间的通信，pod与pod之间的通信）

​		运行时或协调器决定了容器应该加入哪个网络以及它需要调用哪个插件。然后，插件会将接口添加到容器网络名空间中，作为一个veth对的一侧。接着，它会在主机进行更改，包括将veth的其他部分连接到网桥。再之后，它会通过调用单独的IPAM（IP地址管理）插件来分配IP地址并设置路由。

​		在kubernetes中，kubelet可以在适当的时间调用它找到的插件，为通过kubelet启动的pod进行自动的网络配置。

Kubernetes中可选的CNI插件：

- Flannel
- Calico
- Canal
- Weave



### 2.10 Calico

官网 https://docs.projectcalico.org/getting-started/kubernetes/quickstart

kubectl是kubernetes的命令行工具。底层有restful风格API server

```shell
#在master中执行,/usr/local/kubernetes/cluster
kubectl apply -f https://docs.projectcalico.org/v3.8/manifests/calico.yaml

#在master中执行，必须全部出现running才算成功
watch kubectl get pods --all-namespaces

#分别在各个node中，查看镜像是否下载完成
docker images

#在master中执行查看各节点状态是否为ready
kubectl get nodes
```





### 2.11 部署容器

#### 2.11.1 检查状态

```shell
#检查组件运行状态
kubectl get cs

#检查master状态
kubectl cluster-info

#检查node状态
kubectl get nodes
```

#### 2.11.2 部署

```shell
#部署
#类似于docker命令行工具docker run --name nginx -p 80:80 nginx
#1.8后取消了replicas=2，所以只会启动一个
#replicas=2启动两个实例
#port=80，运行在k8s内部的80端口上，但并未映射出来。
kubectl run nginx --image=nginx --replicas=2 --port=80
#1.8以后使用脚本代替
vi /usr/local/kubernetes/nginx-deployment.yaml
#填入内容
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  # 创建2个nginx容器
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
#执行脚本
kubectl apply -f nginx-deployment.yaml
#查看已部署的服务
kubectl get deployment
#查看pod，pod是k8s运行容器的最小单元
kubectl get pods
```

#### 2.11.3 发布

```shell
#发布服务，部署服务暴露端口
kubectl expose deployment nginx --port=80 --type=LoadBalancer
#输出如下：
service/nginx exposed
---------------------------------------------------------------------------------
#查看已发布的服务
kubectl get services
#输出如下：
NAME         TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1     <none>        443/TCP        3d22h
nginx        LoadBalancer   10.96.51.35   <pending>     80:30669/TCP   26s
---------------------------------------------------------------------------------
#查看服务详情
kubectl describe service nginx
#输出如下：
Name:                     nginx
Namespace:                default
Labels:                   app=nginx
Annotations:              <none>
Selector:                 app=nginx
Type:                     LoadBalancer
IP:                       10.96.51.35
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  30669/TCP
Endpoints:                192.168.140.69:80,192.168.141.194:80
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

#### 2.11.4 验证

```shell
#验证是否成功
#在node1和node2中执行，未操作node，但是node中已经有nginx启动，由k8s调度过来
docker ps
docker images
docker exec -it 容器id /bin/bash（进入容器）
whereis nginx（进入后查看安装位置）
cat /usr/share/nginx/html/index.html
#node主机地址+服务详情中NodePort
浏览器访问http://192.168.1.71:30669/
```

#### 2.11.5停止服务

注意：在node中删除容器会一直重启，只有在master中删除掉部署发布的服务，node中才会自动下线。

```shell
#删除已部署服务
kubectl delete deployment nginx
#输出如下：
deployment.extensions "nginx" deleted

#删除已发布服务
kubectl delete service nginx
#输出如下：
service "nginx" deleted
```



### 2.12 通过资源配置运行容器

概述

通过run命令启动容器非常麻烦，docker提供了compose为我们解决了这个问题，而kubernetes是使用kubectl create命令就可以做到和compose一样的效果，该命令可以通过配置文件快速创建一个集群资源对象。



#### 2.12.1 创建YML文件

部署Deployment

```shell
#创建一个名为nginx-deployment.yml配置文件
cd /usr/local/kubernetes/services
vi nginx-deployment.yml
----------------------------------------------
#:set paste粘贴内容
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app
spec:
  # 创建2个nginx容器
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        # 容器标签的名字，发布services时，selector需要在这里对应
        app: nginx
    spec:
      # 配置容器，数组类型可以配置多个
      containers:
      # 容器名称
      - name: nginx
        # 容器镜像
        image: nginx:1.17
        # 暴露端口
        ports:
        - containerPort: 80
---
apiVersion: v1
# 类型，如:Pod/ReplicationController/Deployment/Service/Ingress
kind: Service
metadata:
  name: nginx-http
spec:
  ports:
    ## service暴露的端口
    - port: 80
      # pod上的端口，这里是将service暴露的端口转发到pod端口上
      targetPort: 80
  # 类型
  type: LoadBalancer
  # 标签选择器
  selector:
    #需要和上面部署的deployment名对应
    name: nginx
----------------------------------------------
#部署
kubectl create -f nginx-deployment.yml
#删除
kubectl delete -f nginx-deployment.yml
----------------------------------------------
#查看
kubectl get pods
kubectl get deployment
kubectl get service
----------------------------------------------
#验证，端口号自动分配，从kubectl get service可以看到
192.168.1.70:端口号

```

