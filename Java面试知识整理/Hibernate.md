# Hibernate

### ⭐1. 为什么使用hibernate

- 对JDBC访问数据库的代码进行封装，简化了数据访问层繁琐的重复性代码

- Hibernate是一个基于JDBC的主流持久化框架，是一个优秀的ORM实现，简化了DAO层编码
- Hibernate使用Java反射机制，而不是字节码增强程序来实现透明性
- Hibernate性能非常好，是个轻量级框架，映射的灵活性很出色，支持各种关系型数据库



### ⭐2. 什么是ORM框架

对象——关系映射（Object-Relational Mapping，ORM）：对象和关系数据是业务实体的两种表现形式，业务实体在内存中表现为对象，在数据库中表现为关系数据。内存中的对象之间存在关联和继承关系，而在数据库中，关系数据无法直接表达多对多关联和继承关系。因此，ORM系统一般以中间件形式存在，主要实现程序对象到关系数据库数据的映射。



### 3. hibernate如何在控制台查看打印的SQL语句

##### 3.1 打印SQL语句到控制台

正确的properties配置项：

```properties
spring.jpa.properties.hibernate.show_sql=true          //控制台是否打印
spring.jpa.properties.hibernate.format_sql=true        //格式化sql语句
spring.jpa.properties.hibernate.use_sql_comments=true  //指出是什么操作生成了该语句
```

##### 3.2 打印SQL语句中的参数值

这里使用slf4j的日志，配置文件是logback.xml

```xml
<logger name="org.hibernate.SQL" level="DEBUG"/>
<logger name="org.hibernate.engine.QueryParameters" level="DEBUG"/>
<logger name="org.hibernate.engine.query.HQLQueryPlan" level="DEBUG"/>
```

##### 3.3 打印SQL语句到日志

在上述步骤基础上，在logback.xml中增加两项配置：

```xml
<logger name="org.hibernate.type.descriptor.sql.BasicBinder" level="TRACE"/>
<logger name="org.hibernate.type.descriptor.sql.BasicExtractor" level="TRACE"/>
```



### ⭐4. hibernate的几种查询方式

- HQL查询：面向对象查询操作，主要分类为属性查询、关联查询、分页查询、统计函数、参数查询和命名参数查询
- SQL查询：结构化查询语言，面向数据库表结构
- QBC查询：（Query By Criteria 条件查询）

```java
HQL:  Hibernate Query Language. 面向对象的写法:
Query query = session.createQuery("from Customer where name = ?");
query.setParameter(0, "Bob");
Query.list();

QBC:  Query By Criteria.(条件查询)
Criteria criteria = session.createCriteria(Customer.class);
criteria.add(Restrictions.eq("name", "Ann"));
List<Customer> list = criteria.list();

SQL:
SQLQuery query = session.createSQLQuery("select * from customer");
List<Object[]> list = query.list();

SQLQuery query = session.createSQLQuery("select * from customer");
query.addEntity(Customer.class);
List<Customer> list = query.list();
```

``

### 5. hibernate实体类可以被定义为final吗

可以，但这种做法不好。因为Hibernate会使用代理模式在延迟关联情况下提高性能，如果把实体类定义为final，因为Java不允许对final类进行扩展，所以限制了这个好处。而如果持久化类实现了一个接口而且在该接口中声明了所有定义于实体类中的所有public方法，就能避免出现上述不利后果。



### 6. 在hibernate中使用Integer和int做映射的区别

- 如果将OID定义为Integer类型，Hibernate可以根据其值是否为null来判断一个对象是否为临时的
- 如果将OID定义为int类型，还需要在hbm映射文件中设置其unsaved value属性为0



### ⭐7. hibernate是如何工作的

① 通过 `Configuration config = new Configuration().configure()`读取并解析hibernate.cfg.xml配置文件

② 由hibernate.cfg.xml中的 `<mapping resource="com/xx/User.hbm.xml"/>`读取并解析映射信息

③ 通过 `SessionFactory sf = config.buildSessionFactory()`创建SessionFactory

④ `Session session = sf.openSession()`打开Session

⑤ `Transaction tx = session.beginTransaction()`创建并启动事务Transaction

⑥ persistent operate操作数据，持久化操作

⑦ `tx.commit()`提交事务

⑧ 关闭Session

⑨ 关闭SessionFactory



### 8. get()和load()区别

- load()没有使用对象其他属性时，没有SQL延迟加载
- get()没有使用对象其他属性时，生成了SQL立即加载



### ⭐9. hibernate缓存机制

分为一级缓存和二级缓存

一级缓存是Session级别缓存，在事务范围内有效，是内置的不能被卸载。

二级缓存是SessionFactory级别缓存，从应用启动到应用结束有效，可选，默认没有二级缓存，需要手动开启。

保存数据库后，缓存在内存中保存一份，如果更新了数据库就要同步更新。

🔺适合存放到第二级缓存的数据

很少被修改的数据、经常被查询的数据、不是很重要的数据和允许出现偶尔并发的数据、不会被并发访问的数据、常量数据

⭐hibernate的二级缓存默认不支持分布式缓存，使用memcached、redis等中央缓存来代替二级缓存



### ⭐10. hibernate对象的状态

三种状态：

- Transient（瞬时）：对象刚new出来，还没设置id，设了其他值
- Persistent（持久）：调用了  `save()`、`saveOrUpdate()`，就变成Persistent，有id
- Detached（脱管）：当 session `close()`完之后，就变成Detached  

![image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/Hibernate-Objectstate.webp)  




### 11. 在hibernate中getCurrentSession和openSession区别

- openSession：打开一个新的Session对象，每次使用都是打开一个新的session，使用完需要调用close方法关闭session
- getCurrentSession：获取当前上下文一个session对象，当第一次使用此方法时会自动产生一个session对象，连续使用多次时得到的session都是同一个对象
- 实际开发中，往往使用getCurrentSession多，因为一般是处理同一个事务（使用一个数据库情况）



### 12. hibernate实体类必须要有无参构造函数吗

必须。因为Hibernate框架会调用这个默认构造方法来构造实例对象，即Class类的newInstance方法，这个方法就是通过调用默认构造方法来创建实例对象的。

⭐默认的构造方法不是必须写出来，只在有多个构造方法时必须写出来，因为虚拟机在没有提供任何构造方法时会自动提供默认构造方法（无参构造器）
