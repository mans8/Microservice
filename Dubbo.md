**[dubbo用户手册](http://dubbo.apache.org/zh-cn/docs/user/quick-start.html"With a Title")** 

**[dubbo中文官网](http://dubbo.apache.org/zh-cn/"With a Title")** 

**[dubbo-springboot官方文档](https://github.com/apache/dubbo-spring-boot-project/blob/master/README_CN.md"With a Title")** 

		一款高性能、轻量级的开源java RPC分布式服务框架。提供三大核心能力：面向接口的远程方法调用，智能容错和负载均衡，服务自动注册和发现。最大的特点是使用分层的方式来架构，使用这种方式可以使每个层之间解耦合（或者最大限度解耦合）。从服务模型的角度来看，Dubbo采用的是一种非常简单的模型，要么提供服务，要么消费服务，两种角色。

高可用：崩溃可用；

高性能：JVM，Tomcat，RPC

高并发：垂直扩展，水平扩展

![](E:\千锋教育\【千锋达摩院】微服务架构 2.0（上）Linux + Docker + Kubernetes +SpringBoot + SpringCloudAlibaba\Microservice\picture\dubbo architecture.png)

0.启动服务提供者

1.服务提供者把服务注册到注册中心

2.服务消费者向注册中心订阅服务

3.注册中心与消费者建立异步通信，基于长连接推送变更数据，把watch table的服务ip等元数据发送给消费者（自动发现，观察者模式）

4.消费者调用服务提供者

5.监控

## 1. dubbo组成

| 节点      | 角色说明                               |
| --------- | -------------------------------------- |
| Provider  | 暴露服务提供方                         |
| Consumer  | 调用远程服务的服务消费方               |
| Registry  | 服务注册与发现的注册中心               |
| Monitor   | 统计服务的调用次数和调用时间的监控中心 |
| Container | 服务运行容器                           |



## 2. dubbo功能特点

- **面向接口代理的功性能RPC调用：**提供高性能的基于代理的远程调用能力，服务以接口为粒度，为开发者屏蔽远程调用底层细节
- **智能负载均衡：**内置多种负载均衡策略，智能感知下游节点健康状态，显著减少调用延迟，提高系统吞吐量
- **服务自动注册与发现：**遵循微内核+插件的设计原则，所有核心能力如Protocol、Transport、Serialization被设计成扩展点，平等对待内置实现和第三方实现
- **运行期流量调度：**内置条件、脚本等路由策略，通过配置不同的路由规则，轻松实现灰度发布，同机房优先等功能
- **可视化的服务治理与运维：**提供丰富服务治理、运维工具；随时查询服务元数据、服务健康状态及调用统计，实时下发路由策略、调整配置参数。

## 3. 创建dubbo项目（一级）

创建一个apache dubbo项目，创建pom文件.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
        <relativePath/>
    </parent>
    
    <groupId>com.hgx</groupId>
    <artifactId>apache-dubbo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>pom</packaging>
    
    <modules>
        
    </modules>
    
    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>${java.version}</maven.compiler.source>
        <maven.compiler.target>${java.version}</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
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
                <groupId>com.hgx</groupId>
                <artifactId>apache-dubbo-dependencies</artifactId>
                <version>${peoject.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    
    <profiles>
        <profile>
            <id>default</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <spring-javaformat.version>0.0.1</spring-javaformat.version>
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
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-enforcer-plugin</artifactId>
                        <executions>
                            <execution>
                                <id>enforce-rules</id>
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
                        <groupId>org.apache.maven.plugin</groupId>
                        <artifactId>maven-install-plugin</artifactId>
                        <configuration>
                            <skip>true</skip>
                        </configuration>
                    </plugin>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-javadoc.plugin</artifactId>
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
                <enabled>false</enabled>
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
            <name>Spring Snapshot</name>
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

## 4. 创建统一依赖（二级）

创建目录，pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <groupId>com.hgx</groupId>
    <artifactId>apache-dubbo-dependencies</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>pom</packaging>

    <properties>
        <dubbo.version>2.7.2</dubbo.version>
        <dubbo-actuator.version>2.7.1</dubbo-actuator.version>
        <!-- springboot和springcloud版本是配对springboot2.1.x用Greenwich -->
        <spring-cloud.version>Greenwich.RELWASE</spring-cloud.version>
        <spring-cloud-alibaba.version>0.9.0.RELEASE</spring-cloud-alibaba.version>
        <alibaba-spring-context-support.version>1.0.2</alibaba-spring-context-support.version>
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
            <dependency>
                <groupId>org.apache.dubbo</groupId>
                <artifactId>dubbo</artifactId>
                <version>${dubbo.version}</version>
                <exclusions>
                    <exclusion>
                        <groupId>org.springframework</groupId>
                        <artifactId>spring</artifactId>
                    </exclusion>
                    <exclusion>
                        <groupId>javax.servlet</groupId>
                        <artifactId>servlet-api</artifactId>
                    </exclusion>
                    <exclusion>
                        <groupId>log4j</groupId>
                        <artifactId>log4j</artifactId>
                    </exclusion>
                </exclusions>
            </dependency>
            <dependency>
                <groupId>org.apache.dubbo</groupId>
                <artifactId>dubbo-spring-boot-actuator</artifactId>
                <version>${dubbo.actuator.version}</version>
            </dependency>
            <dependency>
                <groupId>com.alibaba.spring</groupId>
                <artifactId>spring-context-support</artifactId>
                <version>${alibaba-spring-context-support.version}</version>
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

在一级项目的pom文件中加入module



## 5. 创建服务提供者（二级）

​		使用Nacos作为注册中心，Sentinel熔断限流控制中心。不在使用Zookeeper和Dubbo Admin来管理我们的Dubbo应用程序，Dubbo仅作微服务框架中的RPC框架，真正实现对内RPC，对外REST。

### 5.1 pom

创建apache-dubbo-provider，创建pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>com.hgx</groupId>
        <artifactId>apache-dubbo</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>
    
    <artifactId>apache-dubbo-provider</artifactId>
    <packaging>pom</packaging>
    
    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>
    
    <modules>
        <module>apache-dubbo-provider-api</module>
        <module>apache-dubbo-provider-service</module>
    </modules>
</project>
```





### 5.2 创建API接口（三级）

#### 5.2.1 pom

在apache-dubbo-provider下创建apache-dubbo-provider-api目录，创建pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>com.hgx</groupId>
        <artifactId>apache-dubbo-provider</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>
    
    <artifactId>apache-dubbo-provider-api</artifactId>
    <packaging>jar</packaging>
    
    <licenses>
        <license>
            <name>Apache 2.0</name>
            <url>https://www.apache.org/licenses/LICENSE-2.0.txt</url>
        </license>
    </licenses>
     
</project>
```

#### 5.2.2 接口

创建src/main/java，在com.hgx.apache.dubbo.provider.api下创建Interface类EchoService

```java
public interface EchoService {

    String sayHello(String name);

}
```

### 5.3 创建service（三级）

#### 5.3.1 pom

在apache-dubbo-provider下创建apache-dubbo-provider-service目录，创建pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>com.hgx</groupId>
        <artifactId>apache-dubbo-provider</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>
    
    <artifactId>apache-dubbo-provider-service</artifactId>
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
            <artifactId>spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        
        <!-- apache dubbo begin -->
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo-registry-nacos</artifactId>
        </dependency>
        <dependency>
            <groupId>org.alibaba.nacos</groupId>
            <artifactId>nacos-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.alibaba.spring</groupId>
            <artifactId>spring-context-support</artifactId>
        </dependency>
        
        <!-- project begin -->
        <dependency>
            <groupId>com.hgx</groupId>
            <artifactId>apache-dubbo-provider-api</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.hgx.apache.dubbo.provider.ProviderApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
     
</project>
```

#### 5.3.2 启动类

创建src/main/java，在com.hgx.apache.dubbo.provider下创建ProviderApplication

```java
@SpringbootApplication
public class ProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProviderApplication.class,args);
    }
}
```

#### 5.3.3 实现service接口

在com.hgx.apache.dubbo.provider下建service目录

```java
@Service(version = "1.0.0")
public class EchoServiceImpl implements EchoService {

    /**
     * The default value of ${dubbo.application.name} is ${spring.application.name}
     */
    @Value("${dubbo.application.name}")
    private String serviceName;

    @Override
    public String sayHello(String name) {
        return String.format("[%s] : Hello, %s", serviceName, name);
    }
}
```

#### 5.3.4 配置文件

application.yml

```yaml
spring:
  application:
    name: dubbo-provider
  main:
    allow-bean-definition-overriding: true
dubbo:
  scan:
    base-package: com.hgx.apache.dubbo.provider.service
  protocol:
    name: dubbo
    port: -1
  registry:
    address: nacos://192.168.1.55:8848
```

**allow-bean-definition-overriding**指重名类以最后出现为准，**port: -1**指自动分配端口号。

## 6.创建服务消费者（二级）

创建src/main/java

创建src/main/resources

### 6.1 pom

创建apache-dubbo-consumer，创建pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    
    <parent>
        <groupId>com.hgx</groupId>
        <artifactId>apache-dubbo</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>
    
    <artifactId>apache-dubbo-consumer</artifactId>
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
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        
        <!-- apache dubbo begin -->
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-spring-boot-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>dubbo-registry-nacos</artifactId>
        </dependency>
        <dependency>
            <groupId>org.alibaba.nacos</groupId>
            <artifactId>nacos-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.alibaba.spring</groupId>
            <artifactId>spring-context-support</artifactId>
        </dependency>
        
        <!-- project begin -->
        <dependency>
            <groupId>com.hgx</groupId>
            <artifactId>apache-dubbo-provider-api</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.hgx.apache.dubbo.consumer.ConsumerApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
     
</project>
```

在一级项目的pom文件中加入一个module

```xml
<module>apache-dubbo-consumer</module>
```

### 6.2 启动类

创建package名为com.hgx.apache.dubbo.consumer

创建ConsumerApplication类

```java
@SpringbootApplication
public class ConsumerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConsumerApplication.class,args);
    }
}
```

### 6.3 Controller

创建package名为com.hgx.apache.dubbo.consumer.controller

创建EchoController类

```java
@RestController
public class EchoController {
    
    @Reference(version = "1.0.0")
    private EchoService echoService;
    
    @GetMapping(value = "/echo/{string}")
    public String echo(@PathVariable String string) {
        return echoService.echo(string);
    }
}
```

### 6.4 配置文件

```yaml
spring:
  application:
    name: dubbo-consumer
  main:
    allow-bean-definition-overriding: true
dubbo:
  scan:
    base-package: com.hgx.apache.dubbo.consumer.controller
  protocol:
    name: dubbo
    port: -1
  registry:
    address: nacos://192.168.1.55:8848
server:
  port: 8080
endpoint:
  dubbo:
    enabled: true
management:
  health:
    dubbo:
      status:
        defaults: memory
        extras: threadpool
  endpoints:
    web:
      exposure:
        include: "*" 
```

## 7. 验证是否成功

通过浏览器访问Nacos的网址：http://192.168.1.55:8848/nacos/，发现该服务注册在服务中。

服务端点检查访问网址：http://localhost:8080/actuator/health

```json
{
    "status": "UP"
}
```



## 8.dubbo实现高速序列化

序列化：将对象转换为二进制进行传输

反序列化：将二进制转换为对象进行传输

### 8.1 dubbo中的序列化

dubbo RPC是Dubbo体系中最核心的一种高性能、高吞吐量的远程调用方式，可以称之为多路复用的TCP长连接调用：

- **长连接**：避免了多次调用新建TCP连接，提高了调用的响应速度
- **多路复用**：单个TCP长连接可以交替传递多个请求和响应的消息，降低了连接的等待闲置时间，从而减少了同样并发数下的网络连接数，提高了系统吞吐量

Dubbo RPC主要用于两个Dubbo系统之间的远程调用，特别适合高并发、小数据的互联场景。而序列化对于远程调用的响应速度、吞吐量、网络带宽消耗等同样也起着至关重要的作用，是我们提升分布式系统性能的最关键的因素之一。

### 8.2 Dubbo支持的序列化

- **dubbo序列化**：阿里尚未开发成熟的高效java序列化实现，阿里不建议在生产环境使用它。
- **hessian2序列化**：hessian是一种跨语言的高效二进制序列化方式。但这里实际不是原生的hessian序列化，而是阿里修改过的hessian lite，它是**dubbo RPC默认**启用的序列化方式。
- **json序列化**：目前有两种实现，一种是阿里的fastjson，另一种是采用dubbo中自己实现的简单json库，但其实都不是特别成熟，而且json这种文本序列化性能一般且不如上面两种序列化。
- **java序列化**：JDK实现，性能不理想。

### 8.3 Dubbo中选用的序列化

​		Dubbo是java to java的，但hessian是跨语言的，不会单独对java进行优化。最近几年，新的序列化方式不断刷新序列化性能上限，最典型的包括：

- 专门针对java语言的：**Kryo**，FST等
- 跨语言的：Protostuff，ProtoBuf，**Thrift**，Avro，MsgPack等等

> 注意：在面向生产环境中，目前更优先选择Kryo。

### 8.4 启用Kryo序列化

服务提供者kryo，服务消费者Kyro

```xml
<!-- 在统一的dependency中加 -->
<properties>
    <dubbo-kryo.version>2.7.2</dubbo-kryo.version>
</properties>

<!-- 在消费者和提供者项目中加 -->
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-serialization-kryo</artifactId>
    <version>${dubbo-kryo.version}</version>         
</dependency>
```



在响应配置中增加协议

```yaml
dubbo:
  protocol:
    serization: kryo
```

序列化说明	

```java
//想要使用kryo序列化只需要DTO/Domain/Entity这类传输对象实现序列化接口即可，无需额外再做配置，如：
//在对一个类做序列化时，可能级联引用到很多类，如java集合类。针对这种情况，dubbo已经将JDK中的常用类进行了注册。
public class User implements Serializable{}
```



## 9. dubbo负载均衡

dubbo提供多种负载均衡，缺省为random随机调用。

### 9.1 负载均衡策略

- **随机（Random LoadBalance）**：按权重设置随机概率，在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。
- **轮循（RoundRobin LoadBalance）**：按公约后的权重设置轮循比例，存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在第二台上。
- **最少活跃调用数（LeastActive LoadBalance）**：相同活跃数随机，活跃数指调用前后计数差，使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大。
- **一致性Hash（ConsistenHash LoadBalance）**：相同参数的请求总是发到同一提供者。当某一台提供者挂掉时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。

### 9.2 配置负载均衡策略

​		修改dubbo-provider项目的负载均衡策略，默认的负载均衡策略是随机，可配置的值是：random、roundrobin、leastactive、consistenthash

```yaml
dubbo:
  provider:
    loadbalance: roundrobin
```



## 10. dubbo外部化配置Nacos（配置中心）

### 10.1 接入配置中心

在dubbo-consumer中修改pom，引入

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

### 10.2 在Nacos中新建配置

登录nacos中新建配置*dubbo-consumer-config.yaml*

```yaml
spring:
  application:
    name: dubbo-consumer
  main:
    allow-bean-definition-overriding: true
dubbo:
  scan:
    base-package: com.hgx.apache.dubbo.consumer.controller
  protocol:
    name: dubbo
    port: -1
    serialization: kryo
  registry:
    address: nacos://192.168.1.55:8848
server:                                                                                                                                                                                         ； 
  port: 8080
```

### 10.3 修改客户端配置

删除dubbo-consumer的配置文件，创建名为bootstrap.properties的配置文件并删除之前创建的application.yml配置文件

```properties
spring.application.name=dubbo-consumer-config
spring.cloud.nacos.config.server-addr=192.168.1.55:8848
spring.cloud.nacos.config.file-extension=yaml
```

