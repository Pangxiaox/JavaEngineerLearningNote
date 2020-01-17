# Spring/Spring MVC

### 1. 为什么要使用Spring

Spring是一个**轻量级**的**控制反转（IoC）**和**面向切面（AOP）**的容器框架

使用基本的JavaBean代替EJB，并提供了更多的企业应用功能，能够解决企业应用开发的复杂性



### ⭐2. 解释什么是AOP

AOP（Aspect-Oriented Programming），面向切面编程，主要用来解决一些系统层面的问题，比如日志、事务、权限等待等。它可以在不改变原有的逻辑的基础上，增加一些额外功能。

AOP利用一种称为“横切”的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其命名为“Aspect”，即切面。切面，就是那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块之间的耦合度，并有利于未来的可操作性和可维护性。

AOP把软件系统分为两个部分：核心关注点和横切关注点。业务处理主要流程是核心关注点，与之关系不大的是横切关注点。横切关注点一个特点是他们经常发生在核心关注点的多处而且各处基本相似，比如权限认证、日志、事务。AOP作用在于分离系统中的各种关注点，将核心关注点和横切关注点分离开。

目前最受欢迎的AOP库有两个，一个是AspectJ，一个是Spring AOP。

##### AOP核心概念

- Aspect：切面。切面一般定义为一个Java类，每个切面侧重于特定的跨领域功能，比如事务管理或者日志打印等。
- Joinpoint：连接点。程序执行的某个点，比如方法执行、构造函数调用或者字段赋值等。在Spring AOP中，连接点只会有方法调用。
- Advice：通知。在连接点要的代码。
- Pointcut：切点。一个匹配连接点的正则表达式。当一个连接点匹配到切点时，一个关联到这个切点的特定的通知会被执行。
- Weaving：编织。负责将切面和目标对象链接，以创建通知对象，在Spring AOP中没有这个东西。



### ⭐3. 解释什么是IoC

IoC（Inversion of Control），控制反转，借助于“第三方”实现具有依赖关系的对象之间的解耦。  

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/IOC/ioc1.jpg)

软件系统在没有引入IoC容器前，对象A依赖于对象B，对象A在初始化或者运行到某一点时，自己必须主动去创建对象B或者使用已经创建的对象B，无论是创建还是使用对象B，控制权都在自己手中。  

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/IOC/ioc2.jpg)  

软件系统在引入IoC后，对象A与对象B失去了直接联系，当对象A运行到需要对象B的时候，IoC容器会主动创建一个对象B注入到对象A需要的地方。

对象A获得依赖对象B的过程，由主动行为变为被动行为，控制权颠倒过来了。



下面的例子展示了控制反转和依赖注入（DI，Dependency Injection）。

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/IOC/ioc3.png)  

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/IOC/ioc4.png)  

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/IOC/ioc5.png)  

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/IOC/ioc6.png)  

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/IOC/ioc7.png)  

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/IOC/ioc8.png)  



### 4. Spring有哪些主要模块

主要分为核心容器、数据访问/集成、Web、AOP、工具、消息和测试模块。

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/IOC/Spring.webp)  



### ⭐5. Spring常用的注入方式有哪些

Spring通过DI实现IOC，常用的注入方式有三种：

- 构造方法注入
- setter注入
- 基于注解的注入



### 6. Spring中的bean是线程安全的吗

Spring容器中的Bean是否安全，容器本身没有提供Bean的线程安全策略，因此Spring容器中的Bean本身不具备线程安全的特性。



### 7. Spring支持几种bean的作用域

通过spring容器创建一个bean实例时，不仅可以完成bean实例的实例化，还可以为bean指定特定的作用域。

| 类型          | 说明                                                         |
| ------------- | ------------------------------------------------------------ |
| singleton     | 在Spring容器中仅存在一个Bean实例                             |
| prototype     | 每次通过容器的getBean方法获取prototype定义的Bean时，都将产生一个新的Bean实例 |
| request       | 每次HTTP请求都会创建一个新的Bean，仅用于WebApplicationContext环境 |
| session       | 同一个HTTP Session共享一个Bean，不同的HTTP Session使用不同的Bean，仅用于WebApplicationContext环境 |
| globalSession | 同一个全局Session共享一个Bean，用于Portlet，仅用于WebApplicationContext环境 |

其中比较常用的是singleton和prototype两种作用域。

如果不指定Bean的作用域，Spring默认使用singleton作用域。



### 8. Spring自动装配bean有哪些方式

Spring容器负责创建应用程序中的Bean同时通过ID来协调这些对象之间的关系。作为开发人员，需要告诉spring要创建哪些bean并且如何将其装配到一起。

有两种方式进行bean装配：

- 隐式的bean发现机制和自动装配
- 在java代码或者XML中进行显式配置



### 9. Spring事务实现方式

- 编程式事务对于基于POJO的应用来说是唯一选择。编程式事务即允许用户在代码中精确定义事务的边界。
- 基于TransactionProxyFactoryBean的声明式事务管理
- 基于@Transactional的声明式事务管理
- 基于Aspectj AOP配置事务

##### Spring的事务隔离

事务隔离级别指一个事务对数据的修改与另一个并行的事务的隔离程度，当多个事务同时访问相同数据时，如果没有必要的隔离机制，就可能发生以下问题：脏读、幻读、不可重复读。



### 10. Spring MVC运行流程  
![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/IOC/SpringMVC.webp)  

①用户向服务器发送请求，请求被Spring前端控制Servlet DispatcherServlet捕获

②DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI）。然后根据该URI，调用HandlerMapping获得该Handler配置的所有相关的对象（包括Handler对象以及Handler对象对应的拦截器），最后以HandlerExecutionChain对象的形式返回

③DispatcherServlet根据获得的Handler，选择一个合适的HandlerAdapter（如果成功获得HandlerAdapter后，此时将开始执行拦截器的preHandler(...)方法）

④提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller）。在填充Handler的入参过程中，根据个人的配置，Spring将帮你做一些额外工作：

- HttpMessageConverter：将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定的响应信息
- 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等
- 数据格式化：对请求消息进行数据格式化。如将字符串换成格式化数字或者格式化日期等
- 数据验证：验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中

⑤Handler执行完成后，向DispatcherServlet返回一个ModelAndView对象

⑥根据返回的ModelAndView，选择一个适合的ViewResolver（必须是已经注册到Spring容器中的ViewResolver）返回给DispatcherServlet

⑦ViewResolver结合Model和View，来渲染视图

⑧将渲染结果返回给客户端



### 11. Spring MVC有哪些组件

- DispatcherServlet：中央控制器，把请求转发到具体的控制类
- Controller：具体处理请求的控制器
- HandlerMapping：映射处理器，负责映射中央处理器转发给Controller时的映射策略
- ModelAndView：服务层返回的数据和视图层的封装类
- ViewResolver：视图解析器，解析具体的视图
- Interceptors：拦截器，负责拦截我们定义的请求然后做处理工作



### 12. @RequestMapping的作用是什么

@RequestMapping是一个用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径。

@RequestMapping注解有六个属性：

##### value，method

- value：指定请求的实际地址，指定的地址可以是URI Template模式
- method：指定请求的method类型，GET、POST、PUT、DELETE等

##### consumes，produces

- consumes：指定处理请求的提交内容类型（Content-Type），例如application/json，text/html
- produces：指定返回的内容类型，仅当request请求头中的（Accept）类型中包含该指定类型才返回

##### params、headers

- params：指定request中必须包含某些参数值时才让该方法处理
- headers：指定request中必须包含某些指定的header值，才能让该方法处理请求



### 13. @Autowired的作用是什么

@Autowired注解可以对类成员变量、方法以及构造函数进行标注，完成自动装配工作。
