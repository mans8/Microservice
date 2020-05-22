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



