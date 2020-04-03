

基于Docker搭建搭建

## 1.搭建Gitlab（代码管理平台）

```yaml
#在https://hub.docker.com/r/twang2218/gitlab-ce-zh中找gitlab找到汉化的中文版
docker pull twang2218/gitlab-ce-zh（中文版）
docker pull gitlab/gitlab-ce（英文版）

#如果想简单的运行一下看看，可以执行这个命令：
docker run -d -p 3000:80 twang2218/gitlab-ce-zh:11.1.4
#可以将 11.1.4 换成你所需要的版本标签。

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
```



## 2.搭建Nexus（依赖管理平台）

要求硬件的内存达到4G。

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
```





## 3.Registry（镜像管理平台）



