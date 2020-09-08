

Kubernetes中文文档https://kubernetes.io/zh/docs/home/

帮助文档https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/

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



kubernetes里的核心概念

pod

development

service

volumes

pv

pvc

ingress

cni

configmap



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





术语

<hr>

- **节点**：Kubernetes集群中的服务器
- **集群**：Kubernetes管理的一组服务器集合
- **边界路由器**：为局域网和Internet路由数据包的路由器，执行防火墙保护局域网
- **集群网络**：遵循Kubernetes网络模型实现集群内的通信的具体实现，比如Flannel和Calico
- **服务**：Kubernetes的服务（Service）是使用标签选择器标识的一组Pod Service（Deployment）。**除非另有说明，否则服务的虚拟IP仅可在集群内部访问。**





## 3.三种外部访问方式

### 3.1 NodePort（自己调试时用）

​		NodePort服务是引导外部流量到你的服务的最原始方式。NodePort，正如这个名字所示，**在所有节点（虚拟机）上开放一个特定端口**，任何发送到该端口的流量都被转发到对应服务。

NodePort服务特征入下：

- 每个端口都只能是一种服务
- 端口范围只能是30000-32767（可调）
- 不在YAML配置文件中指定则会自动分配一个默认端口

> 建议：不要在生产环境中使用这种方式暴露服务，大多时候我们应该让Kubernetes来选择端口

### 3.2 LoadBalancer（）

​		LoadBalancer服务是暴露服务到Internet的标准方式。所有通往你指定的端口的流量都会被转发到对应的服务。它没有过滤条件，没有路由等。这就意味着你几乎可以发送任何种类的流量到该服务，像HTTP，TCP，UDP，WebSocket，gRPC或其他任意种类。

### 3.3 Ingress（真正使用）

​		Ingress是一种服务类型。相反，它处于多个服务的前端，扮演着“智能路由”或者集群入口的角色。你可以使用Ingress来做许多不同的事，各种不同类型的Ingress控制器也有不同的能力。它允许你基于路径或者子域名来路由流量到后端服务。

​		Ingress可能是暴露服务的最强大方式，但同时也是最复杂的。Ingress控制器有各种类型，包括Google Cloud Load Balance，Nginx，Contour，Istio等等。它有各种插件，比如cert-manager（为服务自动提供SSL证书）。

​		如果你想要使用同一个IP暴露多个服务，这些服务都是使用相同的七层协议（典型如HTTP），你还可以获取各种开箱即用的特性（比如SSL，认证，路由等等）。







## 4.Nginx虚拟主机

​		虚拟主机是一种特殊的软硬件技术，他可以将网络上的每一台计算机分成多个虚拟主机，每个虚拟主机可以独立对外提供www服务，这样就可以实现一台主机对外提供多个web服务，每个虚拟主机之间是独立的，互不影响。

通过Nginx可以实现虚拟主机的配置，Nginx支持三种类型的虚拟机配置

- 基于IP的虚拟主机
- 基于域名的虚拟主机
- 基于端口的虚拟主机

```
#虚拟主机
ROOT www.tomcat.com
myshop www.myshop.com
itoken www.itoken.com

#虚拟目录
localhost
localhost/myshop
localhost/itoken
```





### 4.1 基于docker部署Nginx

```shell
#在node2上部署一个nginx主机
cd /usr/local
mkdir docker
cd docker
mkdir nginx
cd nginx
vi docker-compose.yml
---------------------------------------------------------------
version: '3.1'
services:
  nginx:
    restart: always
    image: nginx
    container_name: nginx
    ports:
      - 80:80
      - 8080:8080
    volumes:
      - ./conf/nginx.conf:/etc/nginx/nginx.conf
      - ./html:/usr/share/nginx/html
---------------------------------------------------------------
#在docker/nginx文件夹下穿件一个配置文件夹
mkdir conf
cd conf
vi nginx.conf
#内容为空保存
```

### 4.2 Nginx配置文件结构

```
# ...
events {
    # ...
}

http {
    # ...
    server{
        # ....
    }
    
    # ...
    server{
        # ...
    }
}
```

> 注意：每一个server就是一个虚拟主机。

### 4.3  基于端口的虚拟主机配置

**需求：**

- Nginx对外提供80和8080两个端口监听服务
- 请求80端口则请求html80目录下的html
- 请求8080端口则请求html8080目录下的html

**操作流程**

- 创建目录及文件，在/usr/local/docker/nginx/html目录下创建html80和html8080两个目录，并分别创建两个index.html文件
- 配置虚拟主机，创建并修改/usr/local/docker/nginx/conf目录下的nginx.conf（记得删除注解）

```json
# 启动进程，通常设置成和CPU的数量相等
worker_processes  2;

events {
    # epoll是多路复用IO中的一种方式
    # 仅适用于linux2.6以上的内核，可以大大提高nginx性能
    use epoll;
    # 单个后台worker process进程的最大并发连接数
    worker_connections  1024;
}

http {
    # 文件扩展名与文件类型映射表
    include       mime.types;
    # 默认文件类型，默认为text/plain
    default_type  application/octet-stream;  
    # 允许sendfile方式传输文件，默认为off，可以在http块，server块，location块。
    sendfile on;
    # 连接超时时间，默认为75s，可以在http，server，location块。
    keepalive_timeout 65;
    # 设定请求缓冲
    client_header_buffer_size    2k;
    # 配置虚拟主机192.168.1.71
    server {
        #监听端口
        listen       80;
        #监听地址（可IP可域名）
        server_name  192.168.1.71;
        location  / {
            #根目录
            root /usr/share/nginx/html/html80;
            #设置默认页
            index index.html index.htm;
        }
    }
    server {
        listen       8080;
        server_name  192.168.1.71;
        location  / {
           root /usr/share/nginx/html/html8080;
           index index.html index.htm;
        } 
    }
}
```

```shell
#通过不同的端口访问到不同的index
cd /usr/local/docker/nginx
mkdir html
cd html
mkdir html80
mkdir html8080
cd html80
vi index.html #内容填个nginx port 80
cd ..
cd html8080
vi index.html #内容填个nginx port 8080
-------------------------------------------------------------------------
#启动
cd /usr/local/docker/nginx
docker-compose up -d

#访问node2
192.168.1.71:80
192.168.1.71:8080

#停止
docker-compose down

#如果容器无法启动则查看日志
docker-compose logs nginx
```



## 5. Nginx反向代理

### 5.1 什么是代理服务器

客户机在发送请求时，不会直接发送给目的主机，而是先发给代理服务器，代理服务器接受客户机请求之后，再向主机发出，并接受目的主机返回的数据，存放在代理服务器的硬盘上，再发送给客户机。



### 5.2 为什么要使用代理服务器

- **提高访问速度：**由于目标主机返回的数据会存放在代理服务器的硬盘中，因此下一次客户再访问相同的站点数据时，会直接从代理服务器的硬盘中读取，起到了缓存作用，尤其对于热门站点能明显提高请求速度。
- **防火墙作用：**由于所有客户机请求都必须通过代理服务器访问远程节点，因此可在代理服务器上设限，过滤某些不安全信息。
- **通过代理服务器访问不能访问的站点：**互联网上有很多开放的代理服务器，客户在访问受限时，可通过不受限的代理服务器访问目标站点，通俗说，我们使用的翻墙浏览器就是利用了代理服务器，虽然不能出国，但也能直接访问外网。



### 5.3 什么是正向代理

本机安装了代理软件，通过这个软件去到了目标服务器。



### 5.4 什么是反向代理

反向代理的服务器架设在服务端，通过缓冲经常被请求的 页面来缓解服务器的工作量，将客户机请求转发给内部网络上的目标服务器，并将从服务器上得到的结果返回给Internet上请求连接的客户端，此时代理服务器与目标主机一起对外表现为一个服务器。





```
#在node2上部署一个tomcat
cd /usr/local/docker/tomcat
vi docker-compose.yml
-----------------------------------------------------------------
version: '3.1'
serices:
  tomcat1:
    image: tomcat
    container_name: tomcat1
    ports:
      - 8081:8080
  tomcat2:
    image: tomcat
    container_name: tomcat2
    ports:
      - 8082:8080

-----------------------------------------------------------------
docker-compose up -d
```



## 6.Nginx负载均衡

​		负载均衡简历在现有网络结构之上，它提供了一种廉价有效透明的方法扩展网络设备和服务器的带宽、增加吞吐量、加强网络数据处理能力、提高网络的灵活性和可用性。负载均衡，英文名成为Load Balance，其意思是分摊到多个操作单元上进行执行。

### 6.1Nginx实现负载均衡

- Nginx作为负载均衡服务器，用户请求先达到Nginx，再由Nginx根据负载配置将请求转发至Tomcat服务器
- Nginx负载均衡服务器：192.168.1.71:80
- Tomcat1服务器：192.168.1.71:8081
- Tomcat2服务器：192.168.1.71:8082
- 修改/usr/local/docker/nginx/conf目录下的nginx.conf配置文件：

```json
user nginx;
worker_processes  1;

events {
    use epoll;
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;  
    sendfile on;
    keepalive_timeout 65;
    client_header_buffer_size    2k;
    
    upstream mypp1 {
        server 192.168.1.71:8081 weight=10;
        server 192.168.1.71:8082 weight=10;
    }
    server {
        listen       80;
        server_name  192.168.1.71;
        location  / {
            proxy_pass http://myapp1;
            index index.jsp index.html index.htm;
        } 
    }
}



```



Nginx Ingress Controller

帮助文档https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/

将入口统一，不再通过LoadBalance等方式将端口暴露出来，而是使用Ingress提供的反向代理负载均衡功能作为我们唯一的入口。

> **注意**：下面包含资源配置的步骤都是自行创建YAML配置文件，通过kubectl create -f <YAML> 或 kubectl apply -f <YAML>部署，kubectl delete -f <YAML>删除



安装Ingress

Ingress Controller有许多种，我们选择最熟悉的Nginx来处理请求，其它可以参考官方文档

- 下载Nginx Ingress Controller配置文件

```shell
#在master上操作
cd /usr/local/kubernetes
mkdir ingress
cd ingress
#下载配置文件
wget https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.34.1/deploy/static/provider/cloud/deploy.yaml
#部署
kubectl apply - f deploy.yaml
#验证是否安装成功
kubectl get pods -n ingress-nginx

#如果地址无法解析
访问https://site.ip138.com/raw.Githubusercontent.com/
输入raw.githubusercontent.com查询IP
vi /etc/hosts
```



## 7. Kubernetes数据卷

​		在Docker中就有数据卷的概念，当删除容器时，数据也会一起被删除，**想要数据持久化就必须把主机上的目录挂载到docker中去**。在k8s中，数据卷是通过Pod实现持久化的，如果Pod删除，数据卷会一起删除，k8s的数据卷是docker数据卷的扩展，k8s适配各种存储系统，包括本地存储EmptyDir，HostPath，网络存储（NFS，GlusterFS，PV/PVC）等。

​		以部署mysql8为例，采用**NFS+PV/PVC**网络存储方案实现kubernetes数据持久化。

### 7.1 NFS

NFS是Network File System，网络文件系统，NFS是FreeBSD中支持的文件系统中的一种，=。NFS基于RPC（Remote Process Call）远程过程调用实现，其允许一个系统在网络上与他人共享目录和文件。通过使用NFS，用户和程序就可以像访问本地文件一样访问远端系统上的文件。NFS是一个非常稳定的，可移植的网络文件系统。具备可扩展和高性能等特性，达到了企业级标准。由于网络速度的增加和延迟的降低，NFS系统一直是通过网络提供文件系统服务的有竞争力的选择。

### 7.2 NFS原理

​		NFS使用RPC进行实现，RPC使得客户端可以调用服务端的函数。同时，由于VFS的存在，客户端可以向使用其它普通文件系统一样使用NFS系统。经由操作系统的内核，将NFS文件系统的调用请求通过TCP/IP发送至服务端的NFS服务，NFS服务器执行相关操作，并将结果返回给客户端。

![avatar](.\picture\NFS.png)

### 7.3 NFS服务主要进程

- rpc.nfsd：最主要的nfs进程，管理客户端是否可登录
- rpc.mountd：挂载和卸载nfs文件系统，包括权限管理
- rpc.lockd：非必要，管理文件锁，避免同时写出错
- rpc.statd：非必要，检查文件一致性，可修复文件



### 7.4 NFS关键工具

- 主要配置文件：`/etc/exports`
- NFS文件系统维护命令：`/usr/bin/exportfs`
- 共享资源的日志文件：`/var/lib/nfs/*tab`
- 客户端查询共享资源命令：`/usr/sbin/showmount`
- 端口配置：`/etc/sysconfig/nfs`



### 7.5 NFS服务端配置

​		在NFS服务器端的主要配置文件为`/etc/exports`时，通过此配置文件可以设置共享文件目录。每条配置记录由NFS共享目录、NFS客户端地址和参数这三部分组成，格式如下：

```
[NFS 共享目录] [NFS 客户端地址1 (参数1, 参数2, 参数 3......)] [客户端地址2 (参数1, 参数2, 参数 3......)]
```

- NFS共享目录：服务器上共享出去的文件目录
- NFS客户端地址：允许其访问的NFS服务器的客户端地址，可以使客户端IP地址，也乐意是一个网段（192.168.140.0/24）
- 访问参数：括号中都逗号分隔项，主要是一些权限选项



### 7.6 访问权限参数

| 序号 | 选项 | 描述                                   |
| ---- | ---- | -------------------------------------- |
| 1    | ro   | 客户端对于共享文件目录为只读权限。默认 |
| 2    | rw   | 客户端对于共享文件目录具有读写权限     |



### 7.7 用户映射参数

| 序号 | 选项           | 描述                                                         |
| ---- | -------------- | ------------------------------------------------------------ |
| 1    | root_squash    | 使客户端使用root账户访问时，服务器映射为服务器本地的匿名账号 |
| 2    | no_root_squash | 客户端连接服务端时如果使用的是root，那么也拥有服务端分享的目录的root权限 |
| 3    | all_squash     | 将所有客户端用户请求映射到匿名用户或用户组（nfsnobody）      |
| 4    | no_all_squash  | 与上相反，默认                                               |
| 5    | anonuid=xxx    | 将远程访问的所有用户都映射为匿名用户，并指定该用户为本地用户（UID=xxx） |
| 6    | anongid=xxx    | 将远程访问的所有用户组都映射为匿名用户组账户，并指定该匿名用户组账户为本地用户组账户（GUI=xxx            ） |

### 7.8 安装NFS服务端



```shell
#创建一个目录作为共享文件夹
mkdir -p /usr/local/kubernetes/volumes

#给目录增加读写权限
chmod a+rw /usr/local/kubernetes/volumes

#安装NFS服务端
apt-get update
apt-get install -y nfs-kernel-server

#配置NFS服务目录，向客户端开放，打开文件
vi /etc/exports

#在尾部增加一行，内容如下
/usr/local/kubernetes/volumes *(rw,sync,no_subtree_check,no_root_squash)
#/usr/local/kubernetes/volumes:作为服务目录向客户端开放
#*:表示任何IP都可以访问
#rw:读写权限
#sync:同步权限
#no_subtree_check:表示如果输出目录是一个子系统，NFS服务器不检查其目录的权限
#no_root_squash:客户端连接服务端时如果使用的是root，那么也拥有对服务端分享目录的root'权限

#重启服务，使配置生效
/etc/init.d/nfs-kernel-server restart
```

### 7.9 安装NFS客户端

```shell
#安装客户端的目的是 验证是否可以上传文件到服务端，安装命令如下
apt-get install -y nfs-common

#创建NFS客户端挂载目录
mkdir -p /usr/local/kubernetes/volumes-mount

#将NFS服务器的/usr/local/kubernetes/volumes目录挂载到NFS客户端的/usr/local/kubernetes/volumes-mount目录
mount 服务端IP:/usr/local/kubernetes/volumes /usr/local/kubernetes/volumes-mount

#使用df命令查看挂载信息
df
```

### 7.10 使用数据卷

​		存储管理与计算管理是两个不同问题。Persistent Volume子系统，对存储的供应和使用做了抽象，以API形式提供给管理员和用户使用，要完成这一任务，我们引入两个新的API资源，**Persistent Volume（持久卷）**和**Persistent Volume Claim（持久卷消费者）**。

​		Persistent Volume（PV）是集群之中一块网络存储。跟Node一样，也是集群资源。PV跟Volume（卷）类似，不过会有独立于Pod的生命周期，这一API对象包括了存储的实现细节，例如：NFS、iSCSI或者其他的云提供商的存储系统。Persistent Volume Claim（PVC）是用户的一个请求。跟Pod类似，Pod消费Node的资源，PVC消费PV的资源。Pod能够申请特定的资源（CPU和内存）；Claim能够请求特定的尺寸和访问模式（例如可以加载一个读写，以及多个只读实例）。

### 7.11 PV与PVC

PV是集群资源。PVC是对这一资源的请求，也是对资源的所有权的检验。PV和PVC的互动遵循如下的生命周期。

- **供应：**集群管理员会创建一系列点PV，这些PV包含了为集群用户提供的真是存储资源，它可以利用Kubernetes API来创建。
- **绑定：**用户创建一个包含了容量和访问模式的持久卷申请。Master会监听OVC产生，并尝试根据请求内容查找匹配的PV，并把PV和PVC进行绑定。用户能够获取满足需要的资源，并且在使用过程中可能超出请求数量。如果找不到合适的卷，这一申请就会持续处于非绑定状态，一直到出现合适的PV。例如一个集群准备了很多的50G持久卷，（虽然总量足够）也是无法响应100G的申请的，除非把100G的PV加入集群。

- **使用：**Pod把申请作为卷来使用。集群    会通过PVC查询绑定的PV，并mount给Pod。对于支持多种访问方式的卷，用户在使用PVC作为作为卷的时候，可以指定需要的访问方式。一旦用户拥有了一个已经绑定的PVC，被绑定的PV就该归该用户所有了。用户的Pods能够通过在Pod的卷中包含的PVC来访问他们占有的PV。

- **释放：**当用户完成对卷的使用时，就可以利用API删除PVC对象了，而且他还可以重新申请。删除PVC后，对应的卷被视为“被释放”，但是这是还不能给其他的PVC使用。之前的PVC数据还保存在卷中，要根据策略进行后续处理。

- **回收：**PV的回收策略向集群阐述了在PVC释放卷的时候，应该如何进行后续工作。目前可以采用三种策略，保留，回收或者删除。保留策略允许重新申请这一资源。在持久卷能够支持的情况下，删除策略会同时删除持久卷以及AWS EBS/GCE PD或者Cinder卷中的存储内容。如果插件能够支持，回收策略会执行基础的删除操作（rm -rf /thevolume/*）



### 7.12 定义PV

持久卷是以插件方式实现的，目前支持的插件如下：

- GCEPeristenDisk
- AWSElasticBlockStore
- NFS（目前采用）
- iSCSI
- RBD（Ceph Block Device）
- Glusterfs
- HostPath（单节点测试使用）
- 本地持久卷

**YAML配置**

创建一个名为nfs-pv-mysql.yml

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: nfs-pv-mysql
spec:
  # 设置容量
  capacity:
    storage: 5Gi
  # 访问模式
  accessModes:
    # 该卷能够以读写模式被多个节点同时加载
    - ReadWriteMany
  # 回收策略，这里是基础删除 'rm -rf/thevolume'
  persistentVolumeReclaimPolicy: Recycle
  nfs:
    # NFS服务端配置的路径
    path: "/usr/local/kubernetes/volumes"
    # NFS服务端地址
    server: 192.168.1.71
    readOnly: false
```

```shell
# 部署
kubectl create -f nfs-pv-mysql.yml
# 删除
kubectl delete -f nfs-pv-mysql.yml
# 查看
kubectl get pv
```

**配置说明**

**Capacity**（容量）

一般来说，PV会指定容量，这里需要使用PV的capcity属性。目前存储大小是唯一一个能够被申请的指标，今后会加入更多属性，例如IOPS，吞吐能力等。

**accessModes**（访问模式，一个卷只加载一种访问模式）

ReadWriteOnce（RWO）：该卷能够以读写模式被加载到一个节点上

ReadWriteMany（ROX）：该卷能够以读写模式被多个节点同时加载

ReadOnlyMany（RWX）：改卷能以只读模式加载到多个节点上



### 7.13 定义PVC

创建一个名为nfs-pvc-mysql-myshop.yml配置文件

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nfs-pv-mysql-myshop
spec:
  accessModes:
  # 需要使用和pv 一致的访问模式
  - ReadWriteMany
  # 按需分配资源
  resources:
     requests:
       storage: 1Gi
```

```shell
# 部署
kubectl create -f nfs-pv-mysql-myshop.yml
# 删除
kubectl delete -f nfs-pv-mysql-myshop.yml
# 查看
kubectl get pvc
```



### 7.14 部署MySQL8

在master中执行

注意：要确保每台Node都安装了NFS客户端，apt-get install -y nfs-common

```yml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: mysql-myshop
spec:
  replicas: 1
  template:
    metadata:
      labels:
        name: mysql-myshop
    spec:
      containers:
        - name: mysql-myshop
          image: mysql:8.0.16
          # 只有镜像不存在时，才会进行镜像拉取
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 3306
          # 同Docker配置中的environment
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: "123456"
          #容器中的挂载目录
          volumeMounts:
            - name: nfs-vol-myshop
              mountPath: /var/lib/mysql
      volumes:
        # 挂载到数据卷
        - name: nfs-vol-myshop
          persistentVolumeClaim:
            claimName: nfs-pvc-mysql-myshop
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-myshop
spec:
  ports:
    - port: 3306
      targetPort: 3306
  type: LoadBalancer
  selector:
    name: mysql-myshop
```

```
kubectl apply -f mysql.yml
kubectl get service
kubectl get deployment
```



```
#kubernetes常用命令
kubectl get pod/service/deployment/ingress/pv/pvc/cm
kubectl apply -f xxx.yml
kubectl delete -f xxx.yml
kubectl describe pod/service/deployment/ingress/pv/pvc/cm
watch kubectl describe pod/service/deployment/ingress/pv/pvc/cm(监控，每隔几秒刷新一次)
```