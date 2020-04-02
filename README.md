# Microservice

事不过三，三则重构，写且只写一次。



## 1.构建单体应用模型

​		开发多个模块，打包成一个war包部署在一台服务器。简单、快捷、避免很多和分布式相关的问题。仅适用用户少情况。

## 2.走向单体地狱（焦油坑）

- 正确修复bug和实现新功能变得复杂。

- 复杂的单体是持续部署的障碍。

- 单体难以扩展。

- 可靠性低，一个bug可能拖垮整个进程。

- 打包部署时间长，发展减缓。


## 3.微服务解决复杂问题

企业开发的复杂即耦合，微服务架构就是解决耦合问题。

一个web应用程序被划分成一组更简单的web应用程序。

一些服务会暴露一个供其它微服务或应用客户端消费的API，其它服务可能实现一个web UI。

实际运行时，每一个服务是一个云虚拟机或者一个docker容器。

服务之间的通信是由API网关负责，API网关负责负载均衡、缓存、访问控制、API计量和监控，可用nginx实现。



### 3.1.SOA架构和微服务架构的区别

 **1.SOA（Service Oriented Architecture）“面向服务的架构”**:他是一种设计方法，其中包含多个服务， 服务之间通过相互依赖最终提供一系列的功能。一个服务 通常以独立的形式存在与操作系统进程中。各个服务之间 通过网络调用。

 **2.微服务架构**:其实**和 SOA 架构类似**,微服务是在 SOA 上做的升华，微服务架构强调的一个重点是“**业务需要彻底的组件化和服务化**”，原有的单个业务系统会拆分为多个可以独立开发、设计、运行的小应用。这些小应用之间通过服务完成交互和集成。

 微服务架构 = 80%的SOA服务架构思想 + 100%的组件化架构思想 + 80%的领域建模思想

## 4.优点

1.解决复杂问题，边界定义明确，单一职责；

2.一个独立的团队专注开发独立服务，可选符合API契约的技术，但更多是用技术选型限制来避免完全混乱状态；

3.实现每个服务的独立部署，持续部署。

4.根据服务实际运行情况，选用最具性价比的硬件。如Compute Optimized实例上部署CPU密集图像处理服务，在Memory Optimized实例上部署一个内存数据库服务。



## 5.缺点

《**没有银弹：软件工程的本质性与附属性工作**》，这篇论文的论述核心是复杂的软件工程无法依靠简单的答案来解决。不要直接向别人要答案，微服务没有直接的答案。

1.微服务是一个分布式系统，其使得整体变得复杂；

2.分区数据库架构；

3.测试微服务应用程序也很复杂；

4.实现了跨越多服务变更；

5.部署微服务应用程序复杂；



## 6.CAP理论和BASE理论

### 6.1CAP理论

​		CAP 理论为：一个分布式系统最多只能同时满足一致性（Consistency）、可用性（Availability）和分区容错性（Partition tolerance）这三项中的两项。

​		2000 年 7 月，加州大学伯克利分校的 Eric Brewer 教授在 ACM PODC 会议上提出 CAP 猜想。2年后，麻省理工学院的 Seth Gilbert 和 Nancy Lynch 从理论上证明了 CAP。之后，CAP 理论正式成为分布式计算领域的公认定理。

​		**一致性（Consistency）**：一致性指 “all nodes see the same data at the same time”，即更新操作成功并返回客户端完成后，所有节点在同一时间的数据完全一致。一致性包含强一致性（通常指这个，一起成功或失败）、弱一致性、顺序一致性。

​		**可用性（Availablility）**：指“Reads and writes always succeed”，即服务一直可用，而且是正常响应时间。

​		**分区容错性（Partition tolerance）**：即分布式系统在遇到某节点或网络分区故障的时候，仍然能够对外提供满足一致性和可用性的服务。

强一致性：同时同步数据

最终一致性：允许延时同步数据



### 6.2CAP理论应用

- 对于大多数互联网场景，主机众多、部署分散，现在的集群规模越来越大，所以节点故障、网络故障时常态，而且要保证服务达到N个9，即保证P和A，舍弃C（退而求其次保证最终一致性）。虽然某些地方影响客户体验，但没达到造成用户流失的严重程度。

- 如果涉及钱财，C必须保证。发生网络故障宁可停止服务，保证CA，舍弃P。例如网络故障只读不写。




### 6.3BASE理论

​		BASE理论是对CAP中一致性和可用性权衡的结果，基于CAP演化而来，

​		核心思想：即使无法做到强一致性，但可根据业务，采用适当的方式来使系统达到最终一致性。与传统事物的ACID特性是相反，完全不同于ACID的强一致性，但往往会结合一起使用。

- **基本可用（Basically Available）**：分布式系统故障时，允许损失部分可用性，即保证核心可用；
- **软状态（Soft state）**：允许数据存在中间状态，并认为不会影响系统的整体可用性。分布式存储中一般一份数据至少有三个副本，允许不同节点间副本同步的延时就是软状态的体现。mysql rellication的异步复制也是一种体现；
- **最终一致性（Eventually consistent）**：不需要保证实时强一致性，只需保证最终数据能一致。弱一致性和强一致性相反，最终一致性是弱一致性的一种特殊情况。



### **6.4ACID和BASE的区别与联系**

​		ACID是传统数据库的常用设计理念，追求强一致性。

​		BASE支持的是大型分布式系统，提出通过牺牲强一致性获得高可用性。

​		ACID代表了两种截然相反的设计哲学，在分布式设计场景中，系统组件对一致性要求是不同的，因此ACID和BASE又会结合使用。



## 7.如何应对高并发？

### 7.1四个指标

- **响应时间（response time）**：系统对请求作出响应的时间，例如处理一个系统处理一个http请求需要200ms，200ms就是响应时间。
- **吞吐量（throughput）**：单位时间内处理的请求数量；

- **QPS（query per second）**：每秒响应请求数；

- **并发用户数**：同时承载正常使用系统功能的用户数。



### **7.2.水平扩展（scale out）**（主要掌握）

​		只要增加服务器数量，就能线性扩充系统性能。需要在架构层进行水平扩展设计。



### **7.3垂直扩展（scale up）**

​		提升单机处理能力。方式主要有两种：

​		1.增加单机硬件性能

​		2.提升单机架构性能；例如使用异步来增加单服务吞吐量，使用无锁数据结构来减少响应时间。

​                                                 

## 8.Linux安装

安装Ubuntu把，mirrors address改成www.mirrors.aliyun/ubuntu

Use An Enrire Disk And Set Up LVM（选LVM未来硬盘才能扩容）

分配磁盘空间要全部用完

**固定IP地址：**

```yml

vi /etc/netplan/50-cloud-init.yaml

network:
    ethernets:
        ens33:
          addresses: [192.168.1.20/24]
          gateway: 192.168.1.2
          nameserver:
            addresses: [192.168.1.2]
    version: 2
    
netplan apply
```



## 9.Linux远程管理

基于口令的安全验证：账号密码远程登录，口令和数据都是加密传输；

基于秘钥的安全验证：创建一对秘钥，公钥方远程服务器上自己的宿主目录中，私钥自己保存。



## 10.Linux目录管理

没有分区，只有磁盘概念。

/代表根目录，~代表当前用户Home目录，.代表当前目录，..代表上级目录

| 目录    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| **bin** | **存放二进制可执行文件（ls，cat，mkdir等），即命令统称为应用程序** |
| boot    | 存放用于系统引导时使用的各种文件                             |
| dev     | 存放设备文件                                                 |
| **etc** | **存放系统配置文件**                                         |
| home    | 存放所有用户文件目录                                         |
| lib     | 存放文件系统中的程序运行需要的共享库及内核模块               |
| mnt     | 系统管理员安装临时文件系统的安装点                           |
| opt     | 额外安装的可选应用程序包所放置的位置                         |
| proc    | 虚拟文件系统，存放当前内存的映射                             |
| root    | 超级用户目录                                                 |
| sbin    | 存放二进制可执行文件，只有root才能访问                       |
| tmp     | 存放临时文件                                                 |
| **usr** | **存放系统应用程序，比较重要的目录/usr/local，本地管理员手动安装软件的目录** |
| **var** | **存放运行时需要改变数据的文件**                             |

一个应程序通常包含：配置文件，数据文件，可执行文件。

例如：Mysql配置文件放/etc/mysql，数据文件放/var/mysql，可执行文件放/bin/mysql，这是软件包安装，如果是源码包安装是装在/usr/local下。

一个箭头>表示重写，两个箭头>>表示追加



## 11.Linux命令

### 11.1常用命令

| 命令  | 作用                   | 参数     | 参数说明                    |                          |
| ----- | ---------------------- | -------- | --------------------------- | ------------------------ |
| ls    | 显示文件和目录         |          | 只列出文件名                | ls                       |
|       |                        | -l       | 详细信息                    | ls -l                    |
|       |                        | -a       | 所有文件，包含隐藏文件      | ls -a                    |
| mkdir | 创建目录               |          |                             | mkdir 目录名             |
|       |                        | -p       | 父目录不存在则先生成父目录  | mkdir 目录名/目录名      |
| cd    | 切换目录               |          |                             | cd home                  |
| touch | 新建文件（空）         |          |                             | touch 123.txt            |
| echo  | 打印字符串             |          |                             | echo “打印内容”          |
|       |                        | >        | 重写文件                    | echo “内容” > 123.txt    |
|       |                        | &gt;&gt; | 追加内容到该文件            | echo “内容” >> 123.txt   |
| cat   | 显示文本文件内容       |          |                             |                          |
| cp    | 复制文件或目录         |          |                             |                          |
| rm    | 删除文件               |          |                             |                          |
|       |                        | -f       | 强制删除                    | rm -f 123.txt            |
|       |                        | -r       | 同时删除该目录下的所有文件  | rm -r lib                |
| mv    | 移动文件或目录         |          |                             |                          |
| find  | 查找文件               |          |                             |                          |
|       |                        | -name    | 根据文件名查找              | find 目录 -name 123.txt  |
| grep  | 指定文件中查找字符串   |          | 用cat加管道\|               | cat 123.txt \| grep 内容 |
| tree  | 树状列出文件           |          |                             |                          |
| pwd   | 显示当前工作目录       |          |                             |                          |
| ln    | 建立软链接（快捷方式） |          |                             | ln 真文件 软链接         |
| more  | 分页显示文本内容       |          |                             |                          |
| head  | 显示文件开头内容       |          |                             |                          |
| tail  | 显示文件结尾内容       |          |                             |                          |
|       |                        | -f       | 跟踪输出                    |                          |
| tar   | 解压缩（术语叫归档）   |          |                             |                          |
|       |                        | -c       | 压缩一个归档文件的参数指令  | tar -czvf 包名.tar.gz .  |
|       |                        | -x       | 解压一个归档文件的参数指令  | tar -xzvf 包名           |
|       |                        | -z       | 是否需要用gzip压缩（常用）  |                          |
|       |                        | -j       | 是否需要用bzip2压缩         |                          |
|       |                        | -v       | 压缩的过程中显示文件        |                          |
|       |                        | -f       | 指定档名，f之后要立即接档名 |                          |
|       |                        | -tf      | 查看归档文件里的文件        |                          |



### 11.2系统管理命令

 linux版任务管理器

| 命令     | 说明                                          |
| -------- | --------------------------------------------- |
| stat     | 显示指定文件的相关信息，比ls命令显示内容更多  |
| who      | 显示在线登录用户                              |
| hostname | 显示主机名称                                  |
| uname    | 显示系统信息                                  |
| top      | 显示当前系统中耗费资源最多的的进程（**CPU**） |
| ps       | 显示瞬间的进程状态                            |
| du       | 显示指定文件（目录）已使用的磁盘空间的总量    |
| df       | 显示文件系统**磁盘**空间的使用情况            |
| free     | 显示当前**内存**和交换空间的使用情况          |
| ifconfig | 显示网络接口信息                              |
| ping     | 测试网络的联通性                              |
| netstat  | 显示网络状态信息（可看**端口**）              |
| clear    | 清屏                                          |
| kill     | 关进程                                        |



## 12.vim编辑器

**运行模式：**

- 编辑模式：等待编辑命令输入
- 插入模式：编辑模式下，输入i进入插入模式，插入文本信息
- 命令模式：在编辑模式下，输入:进入命令模式

**命令模式：**

`:q`：退出

`:qw`：保存退出

`:qw!`：强制保存退出

`:w file`：将当前内容保存为某个文件

`/`：查找字符串

`:set number`：显示行号

`:set nonumber`：关闭显示行号

`:set paste`：原样粘贴



## 13.Linux用户和组管理

​		系统上的用户和组时用来认证和授权的，物以类聚，人以群分，对于权限一致的用户放在同组避免重复冗余（事不过三），放在一个组下面代表用户组下的所有权限（继承）。

**设置初始root密码（如果没有）**

1. 普通账户登录后，输入：`sudo passwd` 
2. 输入新密码，重复输入密码，最后提示passwd：password updated sucessfully



**允许root远程连接**

```shell
vi /etc/ssh/sshd_config

#PermitRootLogin without-password   //注释此行
PermitRootLogin yes    //加入此行

#重启服务
service ssh restart
```



**用户账户说明**

- **超级管理员**：root，默认下Ubuntu系统的root用户不能远程登录；
- **安装时创建的系统用户**：创建时被添加到admin组中，在Ubuntu中，admin组中的用户是可以使用sudo命令来执行只有管理员才能执行的命令。如果不适用sudo，就是一个普通用户；
- **普通用户**：只进行普通操作的用户。

**组账户说明**

**私有组**：当创建一个用户时没有指定属于哪个组，linux就会建立一个与用户同名的私有组，此私有组只含有该用户；即自己给自己分配；

**标准组**：当创建一个用户时可以选定一个标准组，如果一个用户同时属于多个组时，登录后所属的组为主组，其它为附加组。



**账户系文件说明**

/etc/passwd

每一行代表一个账号，众多账号是系统正常运行所必须的，例如bin，nobody每行定义一个用户账户，此文件对所有用户可读。每行账户包含如下信息：

```
hgx@ubuntu:~$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
hgx:x:1000:1000:hgx:/home/hgx:/bin/bash
```

| root:&#120;:0:0:root:/root:/bin/bash |           |                                                              |
| ------------------------------------ | --------- | ------------------------------------------------------------ |
| 用户名                               | root      | 账号，用来对应UID，这里UID是0                                |
| 口令                                 | x         | 密码，早起明文，后放到/etc/shadow中，这里只能看到x           |
| 用户标识符UID                        | 0         | 系统内唯一，root为0，普通用户从1000开始，1-999系统标准账户，500-65536是可登录账号 |
| 组标示号GID                          | 0         | 与/etc/group相关用来规定组名和GID相对应                      |
| 注释                                 | root      | 注释账号                                                     |
| 宿主目录（主文件夹）                 | /root     | 用户登录系统后所进入的目录                                   |
| 命令解释器（shell）                  | /bin/bash | 指定改用户使用的shell，默认是/bin/bash                       |



## 14.Linux文件权限管理

### **14.1权限解析**

r-read（读） w-write（写） x-excute（执行）

| 1位                                   | 2-4位    | 5-7位          | 8-10位       |
| ------------------------------------- | -------- | -------------- | ------------ |
| 文件类型，-指文件，d指目录，l指软链接 | 用户权限 | 用户所在组权限 | 其它用户权限 |

例子：-rw-r--r--  1 root root 14416 Apr 18  2018 libhandle.so.1.0.3

解读：目录，root用户可读可写不可执行，root组仅可读，其它用户仅可读

### 14.2更改文件所有者chown

chown修改文件所有者，change owner

```
chown -R hgx:hgx *  //将目前目录下的所有文件与子目录的拥有者皆设为 hgx，用户组改为hgx:
```



### 14.3更改文件访问权限chmod

mode : 权限设定字串，格式如下 :

```
[ugoa...][[+-=][rwxX]...][,...]
```

- u 表示该文件的拥有者，g 表示与该文件的拥有者属于同一个群体(group)者，o 表示其他以外的人，a 表示这三者皆是。
- \+ 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
- r 表示可读取，w 表示可写入，x 表示可执行，X 表示只有当该文件是个子目录或者该文件已经被设定过为可执行。

其它参数说明：

- -c : 若该文件权限确实已经更改，才显示其更改动作
- -f : 若该文件权限无法被更改也不要显示错误讯息
- -v : 显示权限变更的详细资料
- -R : 对目前目录下的所有文件与子目录进行相同的权限变更(即以递回的方式逐个变更)
- --help : 显示辅助说明
- --version : 显示版本

```
chmod ugo+r file1.txt  //将文件 file1.txt 设为所有人皆可读取
chmod a+r file1.txt  //将文件 file1.txt 设为所有人皆可读取
chmod u+x ex1.py     //将 ex1.py 设定为只有该文件拥有者可以执行
chmod -R a+r *       //将目前目录下的所有文件与子目录皆设为任何人可读取
```

### 14.4更改文件访问权限chmod（数字）

chmod abc file

其中a,b,c各为一个数字，分别表示User、Group、及Other的权限。r=4, w=2, x=1。

- 若要rwx属性则4+2+1=7；
- 若要rw-属性则4+2=6；
- 若要r-x属性则4+1=5。

```
chmod a=rwx file       和    chmod 777 file         效果相同
chmod ug=rwx,o=x file  和    chmod ug=rwx,o=x file  效果相同
```



## 15.Linux软件包管理

### 15.1修改数据源

查看系统版本号lsb_release -a   (LSB是Linux Standard Base)

修改数据源（Ubuntu18.04）

vi /etc/apt/sources.list

```properties
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

apt-get update

### 15.2不同版本

一般来说著名的 Linux 系统基本上分两大类：

1. RedHat 系列：Redhat、Centos、Fedora 等
2. Debian 系列：Debian、Ubuntu 等

- APT（advanced packing tool）是Debian/Ubuntu类Linux系统中的管理程序
- Yum（全称为 Yellow dog Updater, Modified）是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。

| yum命令                                        | apt命令                                                   | 作用                                                         |
| ---------------------------------------------- | --------------------------------------------------------- | ------------------------------------------------------------ |
|                                                | apt-setup                                                 | 设定/etc/apt/souces.list                                     |
|                                                | apt-get update                                            | 软体资料库同步                                               |
| yum install softwarename1 [softwarename2.....] | apt-get install softwarename1 [softwarename2.....]        | 安装软体                                                     |
| yum remove softwarename1 [softwarename2.....]  | apt-get remove softwarename 1 [softwarename 2...]         | 移除软体(保留设定档）                                        |
|                                                | apt-get --purge remove softwarename 1 [softwarename 2...] | 移除软体(不保留设定档）                                      |
|                                                | apt-cache search softwarename                             | 列出所有sofrwarename的套件                                   |
| yum update [softwarename 1 softwarename2...]   | apt-upgrade [softwarename 1 softwarename2...]             | 更新套件，不指定套件名则更新所有可更新的套件                 |
| yum clean                                      | apt-get clean(autoclean)                                  | 删除系统暂存的deb(autoclean只会将比目前系统旧版的套件删除)   |
|                                                | apt-get dist-upgrade                                      | 转换系统的版本（需在/etc/apt/sources.list指定stable，testing或unstable） |



## 16.Linux部署应用程序

### 16.1安装Java

解压并移动到指定目录

1. 解压：`tar -zxvf jdk-8u152-linux-x64.tar.gz`
2. 创建目录：`mkdir -p /usr/local/java/`
3. 移动安装包：`mv jdk1.8.0_152/ /usr/local/java/`
4. 设置所有者：`chown -R root:root /usr/local/java/`

配置环境变量

1. 配置系统环境变量：vi /etc/environment

2. 设置系统环境变量

   ```
   PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
   export JAVA_HOME=/usr/local/java/jdk1.8.0_241
   export JRE_HOME=/usr/local/java/jdk1.8.0_241/jre
   export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
   ```

3. 配置用户环境变量：vi /etc/profile

4. 修改用户环境变量（加21-24行）

   ```shell
   if [ "${PS1-}" ]; then
     if [ "${BASH-}" ] && [ "$BASH" != "/bin/sh" ]; then
       # The file bash.bashrc already sets the default PS1.
       # PS1='\h:\w\$ '
       if [ -f /etc/bash.bashrc ]; then
         . /etc/bash.bashrc
       fi
     else
       if [ "`id -u`" -eq 0 ]; then
         PS1='# '
       else
         PS1='$ '
       fi
     fi
   fi
   
   export JAVA_HOME=/usr/local/java/jdk1.8.0_241
   export JRE_HOME=/usr/local/java/jdk1.8.0_241/jre
   export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
   export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH:$HOME/bin
   
   if [ -d /etc/profile.d ]; then
     for i in /etc/profile.d/*.sh; do
       if [ -r $i ]; then
         . $i
       fi
     done
     unset i
   fi
   ```

5. 使用户环境变量生效：`source /etc/profile`

验证安装是否成功：`java -version`



### 16.2安装tomcat

**解压缩并移动到指定目录**

1. 解压缩：tar -zxvf apache-tomcat-8.5.53.tar.gz
2. 变更目录：mv apache-tomcat-8.5.53 tomcat
3. 移动目录：mv tomcat/ /usr/local

**验证安装是否成功**

启动：`/usr/local/tomcat/bin/startup.sh`

停止：`/usr/local/tomcat/bin/shutdown.sh`



### 16.3安装Mysql

**安装**

1. 更新数据源：`apt-get update`
2. 安装数据库：`apt-get install mysql-server`

> 注意：安装过程中要创建root密码，请选择一个安全密码并记住它。

**配置**

注意：因为是全新安装，您需要运行附带的安全脚本，这会更改一些不太安全的默认选项，例如远程root登录和示例用户。在旧版本的mysql上，您需要手动初始化数据目录，但最新的mysql已经自动完成了。

`mysql_secure_installation`

这将提示您输入您在之前步骤中创建的root密码。您可以按Y，然后enter接受所有后续问题的默认值，但是要询问您是否要更改root密码。您只需要在之前步骤中进行设置即可，因此无需现在更改。

**验证安装是否成功**

`systemctl status mysql`

登录mysql：`mysql -u root -p`

查看数据库：`show databases;`

进入数据库：`use 数据库名`

查看表：`show tables;`

**配置远程访问**（从5.7开始就推荐使用ssh连接，auth_socket）

两种连接方式：

1. mysql_native_password连接（下面要讲的）
2. auth_socket连接

```mysql
#去掉绑定的白名单，对应的错误是2003
修改配置文件 vi /etc/mysql/mysql.conf.d/mysql.cnf
注释掉 #bind-address = 127.0.0.1
重启mysql service mysql restart
```


```mysql
#开启远程登录，对应的错误是1130
mysql -u root -p     #以权限用户root登录
mysql>use mysql;     #选择mysql库
mysql>select 'host' from user where user='root';    #查看mysql库中的user表的host值（即可进行连接访问的主机/IP名称）
mysql>update user set host = '%' where user ='root';   #修改host值（以通配符%的内容增加主机/IP地址），当然也可以直接增加IP地址
mysql>flush privileges;   #刷新MySQL的系统权限相关表
mysql>select 'host' from user where user='root';  #再重新查看user表时，有修改
```

```mysql
#解决无密码不能登录，对应错误1698
>mysql -u root -p
mysql>use mysql
mysql>update user set authentication_string=password('新密码') where user='root';
mysql>update user set plugin="mysql_native_password" where user='root';
mysql>flush privileges;
mysql>exit
>service mysql restart
```



## 17.Linux LVM（Logical volume Manager）



### 17.1LVM基本概念：

- 物理卷Physical Volume（PV）：可以在上面建立卷组的媒介，可以是硬盘分区，也可以是硬盘本身或者回环文件（loopback file）。物理卷包括一个特殊的header，其余部分被切割成一块块物理区域（physical extents）
- 卷组Volume group（VG）：将一组物理卷收集为一个管理单元；VG装着PV；
- 逻辑卷Logical volume（LV）：虚拟分区，由物理区域（physical extents）组成；
- 物理区域Physical extent（PE）：硬盘可供指派给逻辑卷的最小存储单位；
- LVM最小的存储单位是PE（16M，自定义）

> 硬盘的最小存储单位是扇区（512B）
> 文件系统最小的存储单位是block（1K，4K）
> RAID的最小存储单位是chunk（512）

**简单例子：**假设一个系统有6块硬盘（1TB），每块硬盘创建成PV，在PV之上创建一个卷组VG，把PV加入到VG里面，这个VG大小为6TB，LV在这个VG上进行虚拟分区。以后LVM扩容就是，加PV，VG变大，LV分配给相应的虚拟分区。


**LVM和RAID对比：**
- LVM可以分区扩容，RAID扩容的是RAID阵列，不能直接对分区扩容。LVM是逻辑卷托管物理卷。
- 如果用RAID来做，那么一块硬盘多大，就只能使用多大，也就是一个分区最大1TB，分区大小突破硬盘容量，因为实际中分区不会按照硬盘实际大小来分



### 17.2LVM动态扩容

添加硬盘

查看：`df -h`

查看：`fdisk -l`

```shell
#fdisk称为分区工具
root@ubuntu_base:~# fdisk -l
Disk /dev/loop0: 89.1 MiB, 93417472 bytes, 182456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

Disk /dev/sda: 20 GiB, 21474836480 bytes, 41943040 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 997929D5-8CBE-4956-9855-490E8E4B2F25

Device       Start      End  Sectors Size Type
/dev/sda1     2048     4095     2048   1M BIOS boot
/dev/sda2     4096  2101247  2097152   1G Linux filesystem
/dev/sda3  2101248 41940991 39839744  19G Linux filesystem

Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

Disk /dev/mapper/ubuntu--vg-ubuntu--lv: 19 GiB, 20396900352 bytes, 39837696 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

查看所有储存设备：`fdisk -l |grep '/dev'`

```shell
root@ubuntu_base:~# fdisk -l |grep '/dev'
Disk /dev/loop0: 89.1 MiB, 93417472 bytes, 182456 sectors
Disk /dev/sda: 20 GiB, 21474836480 bytes, 41943040 sectors
/dev/sda1     2048     4095     2048   1M BIOS boot
/dev/sda2     4096  2101247  2097152   1G Linux filesystem
/dev/sda3  2101248 41940991 39839744  19G Linux filesystem
Disk /dev/sdb: 10 GiB, 10737418240 bytes, 20971520 sectors
Disk /dev/mapper/ubuntu--vg-ubuntu--lv: 19 GiB, 20396900352 bytes, 39837696 sectors
```

创建adb分区：`fdisk /dev/sdb`

```shell
root@ubuntu_base:~# fdisk /dev/sdb

Welcome to fdisk (util-linux 2.31.1).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x98b3811e.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1): 
First sector (2048-20971519, default 2048): 
Last sector, +sectors or +size{K,M,G,T,P} (2048-20971519, default 20971519): 

Created a new partition 1 of type 'Linux' and of size 10 GiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

```

格式化：`mkfs -t ext4 /dev/sdb1`

```
#格式化分区，磁盘叫sdb，分的第一个区就叫sdb1
root@ubuntu_base:~# mkfs -t ext4 /dev/sdb1
mke2fs 1.44.1 (24-Mar-2018)
Creating filesystem with 2621184 4k blocks and 655360 inodes
Filesystem UUID: c694a358-ff79-4a9b-b656-8a27ae2dd94b
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (16384 blocks): done
Writing superblocks and filesystem accounting information: done
```

|          | 创建     | 扫描   | 查看详细  |
| -------- | -------- | ------ | --------- |
| LV逻辑卷 | lvcreate | lvscan | lvdisplay |
| VG卷组   | vgcreate | vgscan | vgdisplay |
| PV物理卷 | pvcreate | pvscan | pvdisplay |

创建PV：`pvcreate /dev/sdb1`

```shell
#把sdb1变成物理卷
root@ubuntu_base:~# pvcreate /dev/sdb1
WARNING: ext4 signature detected on /dev/sdb1 at offset 1080. Wipe it? [y/n]: y
  Wiping ext4 signature on /dev/sdb1.
  Physical volume "/dev/sdb1" successfully created.
```

扩展到vgextend ubuntu-vg /dev/sdb1

```shell
#把/dev/sdb1这快PV，挂载到ubuntu-vg这个VG上
root@ubuntu_base:~# vgextend ubuntu-vg /dev/sdb1
  Volume group "ubuntu-vg" successfully extended
#扩展前pv
root@ubuntu_base:~# pvscan
  PV /dev/sda3   VG ubuntu-vg       lvm2 [<19.00 GiB / 0    free]
  PV /dev/sdb1                      lvm2 [<10.00 GiB]
  Total: 2 [<29.00 GiB] / in use: 1 [<19.00 GiB] / in no VG: 1 [<10.00 GiB]
#扩展后pv
root@ubuntu_base:~# pvscan
  PV /dev/sda3   VG ubuntu-vg       lvm2 [<19.00 GiB / 0    free]
  PV /dev/sdb1   VG ubuntu-vg       lvm2 [<10.00 GiB / <10.00 GiB free]
  Total: 2 [28.99 GiB] / in use: 2 [28.99 GiB] / in no VG: 0 [0   ]
```

扩容LV

```shell
#/dev/ubuntu-vg/ubuntu-lv路径是lvdisplay信息中的LV Path
#按固定大小追加
lvextend -L +10G /dev/ubuntu-vg/ubuntu-lv

#按百分比追加
lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv

root@ubuntu_base:~# lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
  Size of logical volume ubuntu-vg/ubuntu-lv changed from <19.00 GiB (4863 extents) to 28.99 GiB (7422 extents).
  Logical volume ubuntu-vg/ubuntu-lv successfully resized.
```

刷新LV：`resize2fs /dev/ubuntu-vg/ubuntu-lv`

