# Spring Boot/Spring Cloud

### 1. 什么是spring boot，为什么要用spring boot

在Spring框架这个大家族中产生了很多衍生框架，比如Spring、Spring MVC框架，Spring的核心内容在于控制反转（IOC）和依赖注入（DI），控制反转是指在spring配置文件中创建 `<bean>` ，依赖注入即由Spring容器为应用程序的某个对象提供资源，如引用对象、常量数据等。

SpringBoot是一个框架，一种全新编程规范，简化了Spring众多框架中所需的大量且繁琐的配置文件。

SpringBoot使编码、配置、部署、监控都变简单也弥补了Spring的不足。



### 2. spring boot核心配置文件是什么，配置文件的类型和区别

Spring Boot提供了两种常用的配置文件：

- properties文件
- yml文件：通过空格确定层级关系，使配置文件结构清晰



### ⭐3. spring boot实现热部署的方式

①使用spring loaded

```xml
<build>
        <plugins>
            <plugin>
                <!-- springBoot编译插件-->
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <dependencies>
                    <!-- spring热部署 -->
                    <!-- 该依赖在此处下载不下来，可以放置在build标签外部下载完成后再粘贴进plugin中 -->
                    <dependency>
                        <groupId>org.springframework</groupId>
                        <artifactId>springloaded</artifactId>
                        <version>1.2.6.RELEASE</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
</build>
```

添加完毕后用mvn指令运行

②使用spring-boot-devtools

```xml
 <!--热部署jar-->
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-devtools</artifactId>
 </dependency>
```

然后使用shift+ctrl+alt+“/”，选择Registry，然后勾选compiler.automake.allow.when.app.running



### 4. JPA和Hibernate的区别

- JPA Java Persistence API，是Java EE 5的标准ORM接口，也是Ejb3规范一部分
- Hibernate，当今流行的ORM框架，是JPA的一个实现，但其功能是JPA的超集
- JPA是标准接口，Hibernate是实现，通过三个组件：hibernate-annotation、hibernate-entitymanager、hibernate-core
- hibernate-annotation：Hibernate支持annotation方式配置的基础，包括了标准的JPA annotation以及Hibernate自身特殊功能的annotation
- hibernate-core：Hibernate的核心实现，提供了Hibernate所有的核心功能
- hibernate-entitymanager：实现了标准JPA，可以把它看成hibernate-core和JPA之间的适配器，它并不直接提供ORM的功能，而是对hibernate-core进行封装，使得Hibernate符合JPA的规范



### ⭐5. 什么是spring cloud

Spring Cloud是致力于分布式系统、云服务的框架。

Spring Cloud为开发人员提供了快速构建分布式系统中一些常见模式的工具：

配置管理、服务注册与发现、断路器、智能路由、服务间调用、负载均衡、微代理、控制总线、一次性令牌、全局锁、领导选举、分布式会话、集群状态、分布式消息等

使用Spring Cloud，开发人员可以开箱即用实现这些模式的服务和应用程序。这些服务可以在任何环境下运行，包括分布式环境，也包括开发人员自己的笔记本电脑以及各种托管平台。



### 6. spring cloud断路器的作用

在Spring Cloud中使用了Hystrix来实现断路器功能，断路器可防止一个应用程序多次试图执行一个操作，即很可能失败，允许它继续而不等待故障恢复或者浪费CPU周期，而它确定该故障是持久的。断路器模式也使应用程序能够检测故障是否已经解决，如果问题似乎已经得到纠正，应用程序可以尝试调用操作。

断路器增加了稳定性和灵活性，以一个系统，提供稳定性，而系统从故障中恢复，并尽量减少此故障的对性能的影响。它可以帮助快速地拒绝对一个操作，即很可能失败，而不是等待操作超时（或者不返回）的请求，以保持系统的响应时间。如果断路器提高每次改变状态的时间的事件，该信息可以被用来监测由断路器保护系统的部件的健康状况，或以提醒管理员当断路器跳闸，以在打开状态。



### 7. spring cloud核心组件

①服务发现——Netflix Eureka

一个RESTful服务，用来定位运行在AWS地区（Region）中的中间层服务。由两个组件组成：

- Eureka服务器：用作服务注册服务器
- Eureka客户端：一个java客户端，简化与服务器的交互、作为轮询负载均衡器，并提供服务的故障切换支持

Netflix在其生产环境中使用的是另外的客户端，它提供基于流量、资源利用率以及出错状态的加权负载均衡。

②客户端负载均衡——Netflix Ribbon

主要提供客户侧的软件负载均衡算法。Ribbon客户端组件提供一系列完善的配置选项，比如连接超时、重试、重试算法等。Ribbon内置可插拔、可定制的负载均衡组件。

③断路器——Netflix Hystrix

见第6大点介绍

④服务网关——Netflix Zuul

类似nginx，反向代理的功能，netflix自己增加了一些配合其他组件的特性

⑤分布式配置——Spring Cloud Config

这个是静态的，需要配合Spring Cloud Bus实现动态配置更新

