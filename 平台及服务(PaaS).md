

​		基于Docker搭建Gitlab、Nexus、Harbor。

## 1.搭建Gitlab（代码管理平台）

```yaml
#在https://hub.docker.com/r/twang2218/gitlab-ce-zh中找gitlab找到的中文版
docker pull twang2218/gitlab-ce-zh（中文版）
docker pull gitlab/gitlab-ce（英文版）

#如果想简单的运行一下看看，可以执行这个命令：
docker run -d -p 3000:80 twang2218/gitlab-ce-zh:11.1.4
# 11.1.4 可换成你所需要的版本标签。

vi /usr/local/docker/gitlab/docker-compose.yml
version: '2'
services:
  gitlab:
    image: 'twang2218/gitlab-ce-zh:11.1.4'
    restart: unless-stopped
    hostname: 'http://192.168.0.12' #改成部署gitlab点地址
    environment:
      TZ: 'Asia/Shanghai'
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://192.168.0.12' #改成部署gitlab点地址
        gitlab_rails['time_zone'] = 'Asia/Shanghai'
        gitlab_rails['gitlab_shell_ssh_port'] = 2222 #22可能被其它占用，故用2222
        unicorn['port'] = 8888
        nginx['listen_port'] = 80
        # 需要配置到 gitlab.rb 中的配置可以在这里配置，每个配置一行，注意缩进。
        # 比如下面的电子邮件的配置：
        # gitlab_rails['smtp_enable'] = true
        # gitlab_rails['smtp_address'] = "smtp.exmail.qq.com"
        # gitlab_rails['smtp_port'] = 465
        # gitlab_rails['smtp_user_name'] = "xxxx@xx.com"
        # gitlab_rails['smtp_password'] = "password"
        # gitlab_rails['smtp_authentication'] = "login"
        # gitlab_rails['smtp_enable_starttls_auto'] = true
        # gitlab_rails['smtp_tls'] = true
        # gitlab_rails['gitlab_email_from'] = 'xxxx@xx.com'
    ports:
      - '80:80'
      - '443:443'
      - '2222:22'
    volumes:
      - config:/etc/gitlab
      - data:/var/opt/gitlab
      - logs:/var/log/gitlab
volumes:
  config:
  data:
  logs:

#启动
docker-compose up
#停止
docker-compose down

#访问
ip:80
```



## 2.搭建Nexus（依赖管理平台）

要求硬件的内存达到4G。

本地仓库 -> 指定仓库 -> 官方仓库

登录https://hub.docker.com/r/sonatype/nexus3可查看更详细信息。

```yaml
#拉取镜像
docker pull sonatype/nexus3
#运行
docker run -d -p 8081:8081 --name nexus sonatype/nexus3
#测试
curl http://localhost:8081/

#创建docker compose脚本
vi usr/local/docker/nexus/docker-compose.yml
#插入内容
version: '3.1'
services:
  nexus:
    restart: always
    image: sonatype/nexus3
    container_name: nexus
    ports:
      - 8081:8081
    volumes:
      - /usr/local/docker/nexus/data:/nexus-data

执行启动
docker-compose up -d
如果提示data没权限，赋权限：
chmod 777 /usr/local/docker/nexus/data
#访问
ip:8081

```

### 2.1初始账号密码

```xml
#配置认证信息：初始admin密码在
vi /usr/local/docker/nexus/data/admin.password
#登录后改成123456，一定要在页面改
#在maven配置文件夹下的setting.xml中添加Nexus认证信息（server节点如下）
vi /usr/local/apache-maven-3.6.1/conf/settings.xml
<server>
	<id>nexus-releases</id>
	<username>admin</username>
	<password>123456</password>
</server>
<server>
	<id>nexus-snapshots</id>
	<username>admin</username>
	<password>123456</password>
</server>
```



### 2.2上传代码到Nexus

```xml
#配置自动化部署（上传到私服）：
#在pom.xml文件中添加如下代码
    <distributionManagement> 
        <repository>
            <id>nexus-releases</id>
            <name>Nexus Release Repository</name>
            <url>http://192.168.1.52:8081/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>t 
            <id>nexus-snapshots</id>
            <name>Nexus Snapshot Repository</name>
            <url>http://192.168.1.52:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
#注意：id名称要与setting中server配置的id一致；
#项目版本号中有SNAPSHOT标识的，会发布到Nexus Snapshots Repository，否则发布到Nexus Release Repository，并根据id去匹配授权账号。
#部署命令：
mvn deploy
```

### 2.3从Nexus下载

```xml
<repositories>
    <repository>
        <id>nexus</id>
        <name>Nexus Repository</name>
        <url>http://192.168.124.132:8081/repository/maven-public/</url>
		<!--是否允许依赖快照版-->
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <!--是否允许依赖发行版-->
        <releases>
            <enabled>true</enabled>
        </releases>
    </repository>
</repositories>
<!-- 插件仓库 -->
<pluginRepositories>
    <pluginRepository>
        <id>nexus</id>
        <name>Nexus Plugin Repository</name>
        <url>http://192.168.124.132:8081/repository/maven-public/</url>
        <snapshots>
            <enabled>true</enabled>
        </snapshots>
        <releases>
            <enabled>true</enabled>
        </releases>
    </pluginRepository>
</pluginRepositories>
```



## 3.Docker Compose部署Harbor（镜像管理平台）

​		用于存储和分发Docker镜像点企业级Registry服务器，通过添加一些企业必须点功能特性，例如安全、标识和管理等，扩展了开源Docker Distribution。作为一个企业级私有Registry服务器，Harbor提供了更好点性能和安全。提升用户使用Registry构建和运行环境传输镜像点效率。Harbor支持安装在多个Registry节点点镜像资源复制，镜像全部保存在私有Registry中，确保数据和知识产权在公司内部网络中管控。另外，Harbor提供了高级的安全特性，诸如用户管理、访问控制和活动审计等。

​		harbor地址：https://github.com/goharbor/harbor

### 3.1 Harbor特性

- **基于角色的访问控制：**用户与Docker镜像仓库通过“项目”进行组织管理，一个用户可以对多个镜像仓库在同一命名空间（project）里有不同权限。
- **镜像复制：**镜像可以在多个Registry实例中复制（同步），尤其适合负载均衡，高可用，混合云和多云点场景。
- **图形化用户界面：**用户可以通过浏览器来浏览，检索当前Docker镜像仓库，管理项目的命名空间。
- **AD/LDAP支持：**Harbor可以集成企业内部已有的AD/LDAP，用于鉴权认证管理。
- **审计管理：**所有针对镜像仓库点操作都可以被记录追溯，用于审计管理。
- **国际化：**拥有英文、中文、德文、日文和俄文点本地化版本。
- **RESTful API：**RESTful API提供给管理员对于Harbor更多的操控，使得与其它管理软件集成变得更容易。
- **部署简单：**提供在线和离线两种安装工具，也可以安装到vSphere平台（OVA方式）虚拟设备。



### 3.2 Harbor组件（服务）

- Proxy：Harbor点registry，UI，token等服务，通过一个前置点反向代理统一接收浏览器、Docker客户端的请求，并将请求转发给后端不同的服务。
- Registry：负责存储Docker镜像，并处理docker push/pull命令。由于我们要对用户进行访问控制，即不同用户对Docker image有不同点读写权限，Registry会指向一个token服务，强制用户点每次docker pull/push请求都要携带一个合法的token，Registry会通过公钥对token进行解密验证。
- Core service：这是Harbor核心功能，主要提供以下服务：
  - UI：提供图形化界面，帮助用户管理registry上点镜像（image），并对用户进行授权。
  - WebHook：为了及时获取registry上image状态变化情况，在registry上配置webhook，把状态变化传递给UI模块。（观察者 ）
  - Token：负责根据用户权限给每个docker push/pull命令签发token。Docker客户端向Registry服务发起点请求，如果不包含token，将被重新定向到这里，获得token后再重新想Registry进行请求。
- Database：为core service提供数据库服务，负责存储用户权限、审计日志、Docker image分组信息等数据。
- Job Service：提供镜像远程复制功能，可以把本地镜像同步到其它Harbor实例中。
- Log Collector：为了帮助监控Harbor运行，负责收集其它组件点log，供日后进行分析。



### 3.3 Harbor安装

需要docker 17.06.0-ce+ 和docker-compose 1.18.0+版本

官方github下载最新ilixina安装版并传到linux

```shell
#下载地址
https://github.com/goharbor/harbor/releases
国内镜像http://harbor.orientsoft.cn/

#解压安装包
tar -zxvf harbor-offline-installer-v1.8.0.tgz

#输出如下
harbor/harbor.v1.8.0.tar.gz
harbor/prepare
harbor/LICENSE
harbor/install.sh
harbor/harbor.yml

#配置CA加密步骤（互联网环境要用https，内网不用）
https://goharbor.io/docs/1.10/install-config/configure-https/
# Generate a Certificate Authority Certificate
# 1.Generate a CA certificate private key.
openssl genrsa -out ca.key 4096
# 2.Generate the CA certificate.
openssl req -x509 -new -nodes -sha512 -days 3650 \
 -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=registry" \
 -key ca.key \
 -out ca.crt
# Generate a Server Certificate
# 1.Generate a private key.
openssl genrsa -out registry.key 4096
# 2.Generate a certificate signing request (CSR).
openssl req -sha512 -new \
    -subj "/C=CN/ST=Beijing/L=Beijing/O=example/OU=Personal/CN=registry" \
    -key registry.key \
    -out registry.csr
# 3.Generate an x509 v3 extension file.
cat > v3.ext <<-EOF
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=registry
DNS.2=registry
DNS.3=registry
EOF
# 4.Use the v3.ext file to generate a certificate for your Harbor host.
openssl x509 -req -sha512 -days 3650 \
    -extfile v3.ext \
    -CA ca.crt -CAkey ca.key -CAcreateserial \
    -in registry.csr \
    -out registry.crt

# Provide the Certificates to Harbor and Docker
# 1.Copy the server certificate and key into the certficates folder on your Harbor host.
cp registry.crt /data/cert/
cp registry.key /data/cert/
# 2.Convert yourdomain.com.crt to yourdomain.com.cert, for use by Docker.
openssl x509 -inform PEM -in registry.crt -out registry.cert
# 3.Copy the server certificate, key and CA files into the Docker certificates folder on the Harbor host. You must create the appropriate folders first.
cp registry.cert /etc/docker/certs.d/registry/
cp registry.key /etc/docker/certs.d/registry/
cp ca.crt /etc/docker/certs.d/registry/
# 4.Restart Docker Engine.
systemctl restart docker

# 文档结构
/etc/docker/certs.d/
    └── yourdomain.com:port
       ├── yourdomain.com.cert  <-- Server certificate signed by CA
       ├── yourdomain.com.key   <-- Server key signed by CA
       └── ca.crt               <-- Certificate authority that signed the registry certificate


#修改配置文件
vi harbor.yml或者harbor.cfg

#执行prepare文件
./prepare
#运行./install.sh出现无法生成密文件文件：./ common / config / ui / private_key.pem，证书文件：./ common / config / registry / root.crt
#需要修改prepare文件，将第498行：
empty_subj = "/C=/ST=/L=/O=/CN=/"
#修改为:
empty_subj = "/C=US/ST=California/L=Palo Alto/O=VMware, Inc./OU=Harbor/CN=notarysigner"
#接着设置权限:
chown 10000.10000 ./common/config/registry/root.crt


配置文件说明：https://goharbor.io/docs/1.10/install-config/configure-yml-file/
#修改为域名或你的服务器ip
修改hostname: 192.168.141.150

#执行安装脚本（可能要安装python）
./install.sh

docker-compose up -d
docker-compose down
#查看容器
docker ps | grep vmware

#浏览器输入ip和端口验证

#harbor.cfg或者harbor.yml中有密码
harbor_admin_password = Harbor12345
用户名admin

#配置客户端
vi /etc/docker/daemon.json
#增加内容
{
  "registry-mirrors": ["https://sqymagi8.mirror.aliyuncs.com"],
  "insecure-registries": ["192.168.1.53"]
}
#重启服务
systemctl restart docker
docker-compose start
```

### 3.4使用Harbor管理镜像

```shell
#一个例子
docker pull nginx
docker images

#如果私有的，推送时需要登录
docker login 192.168.1.53 -u admin -p Harbor12345  
docker login 192.168.1.53

#标记
docker tag SOURCE_IMAGE[:TAG] 192.168.1.53/commons/IMAGE[:TAG]
docker tag SOURCE_IMAGE[:TAG] 192.168.1.53/myshop/IMAGE[:TAG]
docker tag nginx:latest 192.168.1.53/myshop/nginx:latest

#推送
docker push 192.168.1.53/commons/IMAGE[:TAG]
docker push 192.168.1.53/myshop/IMAGE[:TAG]
docker push 192.168.1.53/myshop/nginx:latest

#拉取推送完点镜像
docker pull 192.168.1.53/myshop/nginx:latest
```















































































































































































































































































































































































































































































































































































































































































































































































































​	





