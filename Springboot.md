

## 1.Springboot简介

​		随着动态语言都流行（Ruby、Groovy、Scala、Node.js），java显得笨重：配置繁多、开发效率低、部署复杂、第三方集成难度大。

​		Springboot应运而生，“习惯优于配置”（把习惯的配置做成内置，无需手动配置）的理念让你快速运行一个程序。使用springboot容易创建一个独立运行（运行jar内置servlet）准生产级别的基于spring框架都项目，使用springboot可以不使用或者使用很少的spring配置。(集成了大量主流框架的常用配置)

**优点：**

1. 快速构建项目；
2. 对主流开发框架都无配置集成；
3. 项目可独立运行无需servlet容器；
4. 提供运行时都应用监控；
5. 极大提高开发、部署效率；
6. 与云计算的天然集成

**缺点：**

1.    版本迭代快，一些模块改动快

2.    由于无需自己配置，报错难定位（配置是国际惯例或者spring约定）




## 2. 创建一个springboot项目

参考官网：https://spring.io/guides/gs/spring-boot/

![](E:\千锋教育\【千锋达摩院】微服务架构 2.0（上）Linux + Docker + Kubernetes +SpringBoot + SpringCloudAlibaba\Microservice\picture\使用idea创建一个springoot项目.jpg)



## 3. Springboot配置文件

​	Springboot使用一个全局都配置文件application.properties或者是application.yml，在resources目录下或者类路径下都/config下，一般放到resource下。

修改端口为9090，默认访问路径“/”修改为“boot”，在properties中添加：

```properties
server.port=9090
Server.context-path=/boot
```

或者在yml中添加：

```yml
server:
  port: 9090
  context-path: /boot
```

关闭特定的自动配置使用@SpringBootApplication注解的exclude参数即可，以关闭数据源为例：

@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})

配置启动打印信息，在resource下新建banner.txt文件，加入内容。

### 3.1配置数据源

在`src/main/resources`目录下创建`jdbc.properties`数据源配置：

```properties
jdbc.driverClass=com.mysql.cj.jdbc.Driver
jdbc.connectionURL=jdbc:mysql://192.168.1.54:3306/myshop?useUnicode=true&characterEncoding=utf-8&useSSL=false
jdbc.username=root
jdbc.password=123456
```



## 4. Springboot整合Thymeleaf

​		springboot内置tomcat、servlet，因而不支持jsp，jsp是要用外部tomcat。

​		Thymeleaf是一个跟Velocity、FreeMarker类似的模板引擎，可以完全替代jsp。与其它模板引擎相比有三个优点：

1. 有无网络皆可运行，美工可在浏览器中看静态效果，程序员可在服务器查看带数据的动态页面效果。Thymeleaf支持html原型，html引擎在渲染html页面时忽略html非原生的属性，浏览器解释html会忽略未定义的标签属性，所以thymeleaf的模板可以静态运行；当有数据返回页面时，thymeleaf标签会动态地替换掉静态内容使页面动态展示。
2. Thymeleaf开箱即用，提供标准和spring标准两种方言，可以直接套用模板实现JSTL、OGNL表达式效果，避免每天套模板、改标签。
3. Thymeleaf提供spring标准方方言和一个springmvc完美集成模块，可快速实现表单绑定、属性编辑器、国际化等功能。



### **4.1 为什么要使用模板引擎？**

如果希望以jar包形式发布模块，则尽量不要使用jsp相关知识，因为jsp在内嵌的servlet

容器上有一些问题（内嵌tomcat、jetty不支持jar形式运行jsp，undertow不支持jsp）。

Springboot推荐使用thymeleaf模板引擎，因为其提供了完美的sprnigmvc支持。

Springboot提供的模板引擎还包括：**Beetl、Thymeleaf**、FreeMarker、Groovy、Mustache、Velocity。



### 4.2配置Thymeleaf

在pom.xml中配置

```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-thymeleaf</artifactId>
            </dependency>
            <dependency>
                <groupId>net.sourceforge.nekohtml</groupId>
                <artifactId>nekohtml</artifactId>
                <version>1.9.22</version>
            </dependency>
```

在application.yml中配置Thymeleaf

```yaml
spring:
  thymeleaf:
    cache: false #开发时关闭缓存，否则无法看到实时页面
    mode: LEGACYHTML5 # 用非严格点HTML
    encoding: UTF-8
    servlet:
      content-type: text/html
```

修改html标签引入thymeleaf引擎，这样才可以在其它标签里使用th:*语法，声明如下：

```html
<!DOCTYPE html SYSTEM "http://www.thymeleaf.org/dtd/xhtml1-strict-thymeleaf-spring4-4.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
```





## 5. Springboot整合HikariCP


https://github.com/brettwooldridge/HikariCP

​       HikariCP（希卡里[shi ga li] 来自日文，光的意思）是数据库连接池后起之秀，号称性能最好，可以完美pk掉其它连接池。是一个高性能点JDBC连接池，基于BoneCP做了不少改进和优化。超快，快到连Sping Boot2都宣布支持。HikariCP重速度，Druid重监控。C3P0在500个线程后可能会出现异常。

### 5.1为什么需要HikariCP

​		BoneCP作者放弃维护，并在Github项目主页推荐使用HikariCP，产品口号是快速、简单、可靠。

**优化：**

- **字节码精简**：优化代码，知道编译的字节码最少，这样，CPU缓存可以加载更多点程序代码
- **优化代理和拦截器**：减少代码，例如HikariCP的statement proxy只有100行代码，只有BoneCP的十分之一
- **自定义数组类型（FastStatementList）代替ArrayList**：避免每次get()调用都要进行range check，避免调用remove()时的从头到尾点扫描
- **自定义集合类型（ConcurrentBag）**:提高并发读写效率
- **其他针对BoneCP缺陷点优化**：比如对于耗时超过一个CPU时间片的方法调用的研究

**代码量：**

几个连接池点代码量对比（代码量越少，一般意味着执行效率越高，发生bug点可能性越低）

| Pool     | Files | Code  |
| -------- | ----- | ----- |
| Vibur    | 34    | 1927  |
| HikariCP | 21    | 2228  |
| Tomcat   | 31    | 6345  |
| BoneCP   | 49    | 7293  |
| C3P0     | 120   | 15550 |



### 5.2整合HikariCP

**POM**

```xml
<!-- 主要增加HikariCP依赖 -->
<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>${hikaricp.version}</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
    <exclusions>
        <!-- 排除tomcat-jdbc以使用HikariCP -->
        <exclusions>
            <groupId>org.apache.tomcat</groupId>
            <artifactId>tomcat-jdbc</artifactId>
        </exclusions>
    </exclusions>
</dependency>
<!-- MySQL驱动 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>${mysql.version}</version>
</dependency>
```

**application.yml**

```yaml
spring:
  datasource:
    type: com.zaxxer.hikari.HikariDataSource
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://192.168.1.54:3306/myshop?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456
    hikari:
      minimum-idle: 5【】
      idle-timeout: 600000
      maximum-pool-size: 10
      auto-commit: true
      pool-name: MyHikariCP
      max-lifetime: 1800000
      connection-timeout: 30000
      connection-test-query: SELECT 1
```



## 6. Springboot整合TkMyBatis

​		tk.mybatis是在mybatis框架的基础上提供了很多工具，让开发更高效。

### 6.1 pom

在pom.xml中引入mapper-spring-boot-starter依赖，该依赖会自动引入mybatis相关依赖

```xml
<dependency>
    <groupId>tk.mybatis</groupId>
    <artifactId>mapper-spring-boot-starter</artifactId>
    <version>2.1.5</version>
</dependency>
```

### 6.2 application.yml

```
mybatis:
  #实体类点存放路径
  type-aliases-package: com.funtl.hello.spring.boot.domain
  mapper-locations: classpath:mapper/*.xml
```

### 6.3 创建通用父级接口

主要作用是让DAO层点接口继承该接口，以达到使用tk.mybatis的目的

```java
package tk.mybatis.mapper.common.base;
import ...
//此接口不能被扫描到，否则会出错
//spring扫描的方位是有@SpringbootApplication的package指定的范围
@RegisterMapper
public interface MyMapper<T> extends Mapper<T>, MySqlMapper<T> {
}
```

### 6.4 PageHelper

​		Mybatis的分页插件，支持多数据库、多数据源。可以简化数据库的分页查询操作，整合过程也及其简单，只需简单引入依赖即可。封装出总页数、当前页等。

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.12</version>
</dependency>
```

### 6.5 代码生成插件

​		无需编写实体类、DAO、XML配置文件，只需要使用Mybatis提供的一个Maven插件自动生成即可，如果业务复杂只需要修改相关文件即可。

```xml

<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.12</version>
</dependency>

<plugins>
    <plugin>
        <groupId>org.mybatis.generator</groupId>
        <artifactId>mybatis-generator-maven-plugin</artifactId>
        <version>1.3.7</version>
        <configuration>
            <configurationFile>${basedir}/src/main/resources/generator/generatorConfig.xml</configurationFile>
            <overwrite>true</overwrite>
            <verbose>true</verbose>
        </configuration>
        <dependencies>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
            </dependency>
            <dependency>
                <groupId>tk.mybatis</groupId>
                <artifactId>mapper</artifactId>
                <version>4.1.5</version>
            </dependency>
        </dependencies>
    </plugin>
</plugins>



```



```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration PUBLIC
        "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
        
<generatorConfiguration>
    <!-- 数据库 -->
    <properties resource="jdbc.properties"/>

    <context id="Mysql" defaultModelType="flat" targetRuntime="MyBatis3Simple" >
        <property name="beginningDelimiter" value="`"/>
        <property name="endinggDelimiter" value="`"/>
        
        <!-- 配置tk.mybatis插件 -->
        <plugin type="tk.mybatis.mapper.generator.MapperPlugin">
            <property name="mapper" value="tk.mybatis.mapper.MyMapper"/>
        </plugin>
        
        <!-- 配置数据库连接 -->
        <jdbcConnection 
                        driverClass="${jdbc.driverClass}" 
                        connectionURL="${jdbc.connectionURL}" 
                        userId="${jdbc.username}" 
                        password="${jdbc.password}">
        </jdbcConnection>
        
        <!-- 生成实体的位置 -->
        <javaModelGenerator targetPackage="com.funtl.hello.spring.boot.domain" targetProject="src/main/java"/>

        <!-- 生成 XML 的位置 -->
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources"/>
        
        <!-- 生成 DAO 的位置 -->
        <javaClientGenerator 
                             targetPackage="com.funtl.hello.spring.boot.mapper" 
                             targetProject="src/main/java"
                             type="XMLMAPPER"/>
        
        <!-- 设置数据库的表名和实体类名，%代表所有表 -->
        <table tableName="myshop" domainObjectName="%">
            <!-- 默认为false，如果设置为true，在生成点SQL中，table名字不会加上catalog或schema -->
            <property name="ignoreQualifiersAtRuntime" value="true"/>
            <!-- generatedKey用于生成生成主键的方法 -->
            <generatedKey column="id" sqlStatement="Mysql" identity="true"/>
        </table>
    </context>

</generatorConfiguration>

```



















































































































































































































































































































































































































































































































































































