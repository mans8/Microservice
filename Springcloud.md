

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





## 5.创建服务注册中心Nacos

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
    
    //测试负载均衡
    @Value("${server.port}")
    private String port;
    
    @GetMapping(value = "/lb")
    public String lb() {
        return "Provider port:" + port;
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



## 8.Nacos Feign（负载均衡）

​		声明式伪http客户端，它使得写http客户端变得更简单。使用feign只需要创建一个并注解。具有可插拔的注解特性，使用feign注解和JAX-RS注解。Feign支持可插拔的编码器和解码器。Feign默认集成了Ribbon，Nacos兼容Feign，默认实现了负载均衡。

- Feign采用的是基于接口的注解
- Feign整合了Ribbon

### 8.1注入依赖

在spring-cloud-alibaba-consumer项目中增加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

在consumer的主类中增加注解

```java
@SpringBootApplication
@EnableDiscoverClient
@EnableFeignClient
public class ConsumerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class, args);
    }
}
```

### 8.2接口类controller

在consumer下创建目录controller，创建类TestEchoController

```java
@RestController
public class TestEchoController {
    
    @Autowired
    private EchoService echoService;
    
    @GetMapping(value = "/feign/echo/{str}")
    public String echo(@PathVariable String str) {
        return echoService.echo(str);
    }
    
    @GetMapping(value = "/lb")
    public String lb() {
        return echoService.lb();
    }
}
```

在consumer下创建目录service，创建接口EchoService

```java
@FeignClient(name="service-provider")
public interface EchoService {
    @GetMapping(value = "/echo/{string}")
    public String echo(@PathVariable("string") String string);
    
    @GetMapping(value = "/lb")
    public String lb();

}
```



## 9.分布式配置服务中心Nacos

分布式系统中，由于服务数量巨多，为了方便服务配置文件统一管理，实时更新，所以需要分布式配置中心组件。

### 9.1 Nacos Config

Nacos提供用于存储配置和其它元数据的key/value存储，为分布式系统中的外部化配置提供服务器端和客户端支持。使用spring cloud alibaba nacos config，可以在nacos server集中管理spring cloud应用的外部属性配置。

spring cloud alibaba nacos config是spring cloud config server和client的替代方案，客户端和服务器上的概念与spring environment和propertySource有着一致的抽象，在特殊的bootstrap阶段，配置被加载到spring环境中。当应用程序通过部署管道从开发到测试再到生产时，可以管理这些环境之间的配置，并确保应用程序具有迁移时需要运行的所有内容。

### 9.2 创建nacos中的配置文件

以配置consumer为例

在consumer工程中接入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

访问http://192.168.1.55:8848/nacos/，创建一个名为service-consumer-config.yaml配置文件，内容如下：

```yaml
#配置不能有注释
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
user:
  name: "任意一个名字"
```

在工程中把resources下的yml配置文件删除，在resources下新建bootstrap.properties，内容如下：

```properties
# nacos中配置的文件名
spring.application.name=service-consumer-config
# nacos的地址和端口
spring.cloud.nacos.config.server-addr=192.168.1.55:8848
# nacos中配置的文件类型
spring.cloud.nacos.config.file-extension=yaml
```

springboot加载配置文件的优先级：bootstrap.properties -> bootstrap.yml -> application.properties -> application.yml

### 9.3 加注解

在TestEchiController

```java
@RefreshScope
@RestController
public class TestEchoController {
    
    @Autowired
    private EchoService echoService;
    
    @GetMapping(value = "/feign/echo/{str}")
    public String echo(@PathVariable String str) {
        return echoService.echo(str);
    }
    //测试负载均衡
    @GetMapping(value = "/lb")
    public String lb() {
        return echoService.lb();
    }
    //测试配置中心
    @Value("${user.name}")
    private String username;
    @GetMapping(value = "/feign/echo")
    public String echo() {
        return echoService.echo(username);
    }
}
```

### 9.4 测试配置中心是否生效

访问192.168.1.55:8080/feign/echo，会返回username。



## 10.多环境配置

​		三套环境：开发、测试、生产。spring为我们提供了springboot profile这个功能（maven也为我们提供了maven profile），只需要在启动时添加一个虚拟机参数，激活自己环境所要用的profile就可以了.



### 10.1 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

### 10.1 编写专门配置文件

在nacos中新增配置文件文件和在resources下新建properties

- service-provider-config.yaml对应bootstrap.properties
- service-provider-config-test.yaml对应bootstrap-test.properties
- service-provider-config-prod.yaml对应bootstrap-prod.properties

```yaml
#service-provider-config.yaml(默认开发环境)
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

# bootstrap.properties(默认开发环境)
spring.application.name=service-provider-config
spring.cloud.nacos.config.server-addr=192.168.1.55:8848
spring.cloud.nacos.config.file-extension=yaml

-------------------------------------------------------------------------------------------
#service-provider-config-prod.yaml（生产环境）
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
  port: 8071
management:
  # 端点检查（健康检查）
  endpoints:
    web:
      exposure:
        include: "*"

# bootstrap.properties(生产环境)
spring.profiles.active=prod
spring.application.name=service-provider-config
spring.cloud.nacos.config.server-addr=192.168.1.55:8848
spring.cloud.nacos.config.file-extension=yaml
```

### 10.2 添加启动参数

```shell
#开发时在idea中启动，在run configuration的active profiles中填写prod

#打包启动项目时添加一个命令参数--spring.profile.active=prod
java -jar 1.0.0-SNAPSHOT.jar --spring.profile.active=prod
```



## 11.Sentinel分布式系统的流量防卫兵

​		在微服务架构中，根据业务来拆分成一个个服务，服务与服务之间可以通过http/rpc相互调用，在springcloud中可以用restTemplate + LoadBalanceClient和Feign来调用。为了保证其高可用，当个服务通常会集群部署。由于网络或自身原因，服务并不能100%可用，如果单个服务出现问题，调用这个服务就会出现现成阻塞，此时若有大量请求涌入，servlet容器的线程资源就会被消耗完毕，导致服务瘫痪。服务与服务之间点依赖性，故障会传播，会对整个微服务系统造成灾难性的严重后果，这就是服务故障的“雪崩”效应。为了解决这个问题，业界提出了熔断器模型。

​		阿里巴巴开源了sentinel组件，实现了熔断器模式，springcloud对这一组件进行了整合。在微服务架构中，一个请求需要调用多个服务是非常常见的。

​		熔断器打开后，为了避免连锁故障，通过fallback方法可以直接返回一个固定值。



### 11.1 Sentinel特征

​		Sentinel以流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。

**特征**：

- **丰富的应用场景：**Sentinel承接了阿里巴巴近10年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷（对于突然到来的大量请求，您可以配置流控规则，以稳定的速度逐步处理这些请求，从而避免流量突刺造成系统负载过高）、集群流量控制、实时熔断下游不可用应用等。
- **完备的实时监控：**Sentinel同时提供实时监控功能。可以在控制台中看到接入应用的单台机器秒级数据，甚至500台以下规模的集群的汇总运行情况。
- **广泛的开源生态：**Sentinel提供开箱即用的与其它开源框架、库的整合模块，例如与Spring Cloud、Dubbo、gRPC的整合。您只需要引入相应的依赖并进行简单的配置即可快速接入Sentinel。
- **完善的SPI扩展点：**Sentinel提供简单易用、完善的SPI扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源

![](E:\千锋教育\【千锋达摩院】微服务架构 2.0（上）Linux + Docker + Kubernetes +SpringBoot + SpringCloudAlibaba\Microservice\picture\Sentinel主要特性.png)

![](E:\千锋教育\【千锋达摩院】微服务架构 2.0（上）Linux + Docker + Kubernetes +SpringBoot + SpringCloudAlibaba\Microservice\picture\Sentinel开源生态.png)

### 11.2 Sentinel控制台

​			Sentinel提供一个轻量级的开源控制台，它提供机器发现以及健康管理、监控（单机和集群），规则管理和推送的功能。另外，鉴权在生产环境中也必不可少。这里，我们将会详细讲述如何通过简单的步骤就可以使用这些功能。Sentinel控制台最少应该包含如下功能：

- **查看机器列表以及健康情况**：收集Sentinel客户端发送的心跳包，用于判断机器是否在线。
- **监控（单机和集群聚合）**：通过Sentinel客户端暴露的监控API，定期拉取并且聚合应用监控信息，最终可以实现秒级的实时监控。
- **规则管理和推送**：统一管理推送规则。
- **鉴权**：生产环境中鉴权非常重要。这里每个开发者需要根据自己的实际情况进行定制。

```shell
#获取
从https://github.com/alibaba/Sentinel/releases下载

#启动（需要jdk 1.8以上）
java -Dserver.port=8080 -Dcsp.sentinel.dashboard.server=localhost:8080 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard-1.7.2.jar

#鉴权
#如果省略这两个参数，账号密码默认为sentinel
-Dsentinel.dashboard.auth.username=sentinel用于指定控制台的登录用户名为sentinel
-Dsentinel.dashboard.auth.password=123465用于指定密码为123465

#访问
localhost:8080
```

### 11.3 Sentinel客户端接入

在工程的配置中追加

```yaml
spring:
  cloud:
    sentinel:
      transport:
        dashboard: localhost:8888
feign:
  sentinel:
    enabled: true
```

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId> 
</dependency>
```

### 11.4测试fallback

在consumer中添加

```java
@Component
public class EchoServiceFallback implements EchoService {
    @Override
    public String echo(String string) {
        return "网络有问题";
    }
    @Override
    public String lb() {
        return "请联系管理员";
    }
}
```

```java
@FeignClient(value = "service-provider", fallback = EchoServiceFallback.class)
public interface EchoService {
    @GetMapping(value = "/echo/{string}")
    public String echo(@PathVariable("string") String string);
    
    @GetMapping(value = "/lb")
    public String lb();
}
```

启动consumer，不启动provider，访问localhost:8080/feign/echo/hi

