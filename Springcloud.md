

## 1. SpringCloud Netflix

以下Spring Cloud Netflix模块和相应的starter将进入维护模式：

- spring-cloud-netflix-archaius
- spring-cloud-netflix-hystrix-contract
- spring-cloud-netflix-hystrix-dashboard
- spring-cloud-netflix-hystrix-stream
- spring-cloud-netflix-hystrix
- spring-cloud-netflix-ribbon
- spring-cloud-netflix-turbine-stream
- spring-cloud-netflix-turbine
- spring-cloud-netflix-zuul







## 2. SpringCloud Alibaba

​		 [Spring Cloud Alibaba 官方github](https://github.com/alibaba/spring-cloud-alibaba "With a Title"). 

​		2018年10月31日springcloud进入springcloud官方孵化器。

​		致力于提供微服务开发的一站式解决方案。此项目包含开发分布式应用微服务的必须组件，方便开发者通过springcloud编程模型轻松使用这些组件来开发分布式应用服务。

​		依托springcloud Alibaba，只需要添加少量注解和少量配置，就可以将springcloud应用接入阿里微服务解决方案，通过阿里中间件来迅速搭建分布式应用系统。





### 2.1 主要功能

- 服务限流降级：默认支持Servlet、Feign、RestTemplate、Dubbo和RocketMQ限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级Metrics监控；
- 服务注册与发现：适配springcloud服务注册与发现标准，默认集成了Ribbon点支持；
- 分布式配置与管理：支持分布式系统中的外部化配置，配置更改时自动刷新；
- 消息驱动能力：基于springcloud Stream为微服务应用构建消息驱动能力；（异步消息）
- 阿里云对象存储：阿里云提供的海量、安全、低成本、高可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型点数据；（分布式存储）
- 分布式任务调度：提供秒级、精准、高可靠、高可用的定时（基于Cron表达式）任务调度服务。同时提供分布式的任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有worker（schedulerx-client）上执行。





### 2.2 组件

- Sentinel：把流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性；
- Nacos：一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台；（服务配置与发现，配置中心）
- RocketMQ：一款开源的分布式消息系统，基于高可用分布式集群技术，提供低延时的、高可靠的消息发布与订阅服务；
- Dubbo： Apache Dubbo是一款轻量级、高性能的RPC框架；
- Seata：阿里巴巴开源产品，一个易于使用的高性能微服务分布式事务解决方案；（分布式事务）
- Alibaba Cloud ACM：一款分布式架构环境中对应用配置进行集中管理和推送的应用配置中心产品；
- Alibaba Cloud OSS：阿里云对象存储服务（Object Storage Service，简称OSS），是阿里云提供点海量、安全、低成本、高可靠的云存储服务。可以在任何应用、任何时间、任何地点存储和访问任意类型的数据；
- Alibaba Cloud SchedulerX：阿里中间件团队开发的一款分布式任务调度产品，提供秒级、精准、高可靠、高可用的定时（基于Cron表达式）任务调度服务。
- Alibaba Cloud SMS：覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。



## 3.创建spring-cloud项目（一级）

创建springcloud项目，删除src目录，因为此工程是用来装微服务的，没有代码。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
    </parent>

    <groupId>com.hgx</groupId>
    <artifactId>spring-cloud-alibaba</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <!-- 默认是jar，还有war -->
    <packing>pom</packing>

    <properties>
        <!--environment setting-->
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    </properties>
    
    <!-- 每加一个工程，就在此处引入 -->
    <modules>
        <module>spring-cloud-alibaba-dependencies</module>
    </modules>
    
    <dependencies>
    	<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>

        </plugins>
    </build>

    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>
    
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.hgx</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <!-- 这种写法是我的pom里面又多了一个pom -->
                <scope>import</scope>
            </dependency>        
        </dependencies>

    </dependencyManagement>
    
    <!-- 多环境配置profiles -->
    <profiles>
        <profile>
            <!-- 换成test就是测试环境 -->
            <id>default</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <!-- 在环境中用的properties  -->
            <properties>
                <spring-javaformat.version>0.0.12</spring-javaformat.version>
            </properties>
            
            <build>
                <plugins>
                    <plugin>
                    	<groupId>io.spring.javaformat</groupId>
                		<artifactId>spring-javaformat-maven-plugin</artifactId>
                        <version>${spring-javaformat.version}</version>
                    </plugin>
                    <plugin>
                    	<groupId>org.apache.maven.plugin</groupId>
                		<artifactId>maven-surefire-plugin</artifactId>
                        <configuration>
                            <includes>
                                <include>**/*Tests.java</include>
                            </includes>
                            <excludes>
                                <exclude>**/Abstract*.java</exclude>
                            </excludes>
                            <systemPropertyVariables>
                                <java.security.edg>file:/dev/./urandom</java.security.edg>
                                <java.awt.headless>true</java.awt.headless>
                            </systemPropertyVariables>
                        </configuration>
                    </plugin>
                    
                    <plugin>
                    	<groupId>org.apache.maven.plugin</groupId>
                		<artifactId>maven-enforcer-plugin</artifactId>
                        <executions>
                            <execution>
                                <id>enfore-rules</id>
                                <goals>
                                    <goal>enforce</goal>
                                </goals>
                                <configuration>
                                    <rules>
                                        <bannedDependencies>
                                            <excludes>
                                                <exclude>commons-logging:*:*</exclude>
                                            </excludes>
                                            <searchTransitive>true</searchTransitive>
                                        </bannedDependencies>
                                    </rules>
                                    <fail>true</fail>
                                </configuration>
                            </execution>
                        </executions>
                    </plugin>      
                    
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-install-plugin</artifactId>
                        <configuration>
                            <skip>true</skip>
                        </configuration>
                    </plugin>
                    
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-javadoc-plugin</artifactId>
                        <configuration>
                            <skip>true</skip>
                        </configuration>
                        <inherited>true</inherited>
                    </plugin>
                    
                </plugins>
            </build>
        </profile>
    </profiles>
    
    <repositories>
        <repository>
            <id>spring-milestone</id>
            <name>Spring Milestone</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        
        <repository>
            <id>spring-snapshot</id>
            <name>Spring Snapshot</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    
    <pluginRepositories>
        <pluginRepository>
            <id>spring-milestone</id>
            <name>Spring Milestone</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>true</enabled>           
            </snapshots>
        </pluginRepository>
        <pluginRepository>
            <id>spring-snapshot</id>
            <name>Spring Snapshot</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>           
            </snapshots>
        </pluginRepository>    
    </pluginRepositories>

</project>
```



## 4.创建统一的依赖管理

在spring-cloud下新建目录，新建pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.hgx</groupId>
    <artifactId>spring-cloud-dependencies</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>spring-cloud-dependencies</name>

    <properties>
        <!-- springboot和springcloud版本是配对springboot2.1.x用Greenwich -->
        <spring-cloud.version>Greenwich.RELWASE</spring-cloud.version>
        <spring-cloud-alibaba.version>0.9.0.RELEASE</spring-cloud-alibaba.version>
    </properties>
    
    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>
    
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring-cloud-alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>   
    
    <repositories>
        <repository>
            <id>spring-milestone</id>
            <name>Spring Milestone</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-snapshot</id>
            <name>Spring Milestone</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
    </repositories>
    
    <pluginRepositories>
        <pluginRepository>
            <id>spring-milestone</id>
            <name>Spring Milestone</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </pluginRepository>
        <pluginRepository>
            <id>spring-snapshot</id>
            <name>Spring Snapshot</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </pluginRepository>   
    </pluginRepositories>

</project>

```





## 5.创建注册中心Nacos

 **[Nacos官方github](https://github.com/alibaba/Nacos "With a Title").**   **[Nacos官方文档](https://nacos.io/zh-cn/docs/what-is-nacos.html "With a Title").** 

​		Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。

​		Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。

![](E:\千锋教育\【千锋达摩院】微服务架构 2.0（上）Linux + Docker + Kubernetes +SpringBoot + SpringCloudAlibaba\Microservice\picture\nacosMap.jpg)



### 5.1关键特性

- 服务发现和服务健康监测
- 动态配置服务
- 动态 DNS 服务
- 服务及其元数据管理



### 5.2 安装Nacos（docker安装）

**[基于docker安装Nacos官方文档](https://nacos.io/zh-cn/docs/quick-start-docker.html"With a Title").** 

```shell

mkdir /usr/local/docker
cd /usr/local/docker
git clone https://github.com/nacos-group/nacos-docker.git
cd /usr/local/docker/nacos-docker/example

#由于文件名不叫docker-compose.yml，不能用docker-compose up 启动
#单机模式（可用于测试和单机使用，生产环境切忌使用单机模式（满足不了高可用））
#单机模式 Derby（内嵌的数据库，通过命令直接启动即可，无需额外安装。）
docker-compose -f ./standalone-derby.yaml up

#单机模式 Mysql（MySQL数据）
docker-compose -f ./standalone-mysql-8.yaml up
docker-compose -f ./standalone-mysql-5.7.yaml up

#集群模式（用于生产环境，确保高可用）
docker-compose -f ./cluster-hostname.yaml up 

#查看
docker ps

#访问
http://192.168.1.55:8848/nacos/
#用户名nacos，密码nacos
#查看启动日志
docker-compose -f ./standalone-mysql-5.7.yaml logs -f
```





## 6.创建服务提供者

​		提供可复用和可调用服务的应用方

### 6.1 pom

创建spring-cloud-alibaba-provider目录，新建pom文件，在一级项目的module中加入

```xml
<module>spring-cloud-alibaba-provider<module>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>com.hgx</groupId>
        <artifactId>spring-cloud-alibaba</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath>../pom.xml</relativePath>
    </parent>
    
    <artifactId>spring-cloud-alibaba-provider</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    
    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>
    
    <dependencies>
        <!-- spring boot begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <!-- 做监控的 -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
        
        <!-- spring cloud begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.hgx.spring.cloud.alibaba.provider.ProviderApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

### 6.2 配置yml

创建src/main/java和src/main/resources

在resources目录下创建application.yml

```yaml
spring:
  application:
    #跟项目名一样，且不能重名
    name: service-provider
  cloud:
    nacos:
      discovery:
        # 服务注册中心
        server-addr: 192.168.1.55:8848
server:
  # 服务端口
  port: 8070
management:
  # 端点检查（健康检查）
  endpoints:
    web:
      exposure:
        include: "*"
```

### 6.3 主类

在java目录下创建com.hgx.spring.cloud.alibaba.provider，创建ProviderApplication类

```java
@SpringBootApplication
@EnableDiscoverClient
public class ProviderApplication {
    public static void main(String[] args) {
        SpringApplication.run(ProviderApplication.class, args);
    }
}
```

### 6.4 接口类controller

在java目录下创建controller文件夹，创建EchoController类

```java
@RestController
public class EchoController {
	@GetMapping(value = "/echo/{string}")
    public String echo(@PathVariable("string") String string) {
        return "return" + string;
    }
}
```

运行主类

访问http://192.168.1.55:8070/echo/hi访问接口

访问 http://192.168.1.55:8848/nacos/查看服务



## 7.创建服务消费者

会发起某个服务调用的应用方。

### 7.1 pom

创建spring-cloud-alibaba-consumer目录，新建pom文件，在一级项目的module中加入

```xml
<module>spring-cloud-alibaba-consumer<module>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>com.hgx</groupId>
        <artifactId>spring-cloud-alibaba</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <relativePath>../pom.xml</relativePath>
    </parent>
    
    <artifactId>spring-cloud-alibaba-consumer</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>jar</packaging>
    
    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>
    
    <dependencies>
        <!-- spring boot begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <!-- 做监控的 -->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>
        
        <!-- spring cloud begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.hgx.spring.cloud.alibaba.consumer.ProviderApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

### 7.2 配置yml

创建src/main/java和src/main/resources

在resources目录下创建application.yml

```yaml
spring:
  application:
    #跟项目名一样，且不能重名
    name: service-consumer
  cloud:
    nacos:
      discovery:
        # 服务注册中心
        server-addr: 192.168.1.55:8848
server:
  # 服务端口
  port: 8080
management:
  # 端点检查（健康检查）
  endpoints:
    web:
      exposure:
        include: "*"
```

### 7.3 主类

在java目录下创建com.hgx.spring.cloud.alibaba.consumer，创建ConsumerApplication类

```java
@SpringBootApplication
@EnableDiscoverClient
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

### 7.4 配置类

在consumer下创建目录configure，创建类ConsumerConfiguration

```java
@Configuration
public class ConsumerConfiguration {
	//等于在配置文件中加入
	//<bean id="restTemplate" class="org.springframework.web.client.RestTemplate">
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```



### 7.5接口类controller

在consumer下创建目录controller，创建类TestController

```java
@RestController
public class TestController {
    
    private final RestTemplate restTemplate;
    
    @Autowired
    public TestController(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }
    
	@GetMapping(value = "/echo/{str}")
    public String echo(@PathVariable("str") String string) {
        return restTemplate.getForObject("http://service-provider/echo/" + str, String.class);
    }
}
```









