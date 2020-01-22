# MyBatis

### 1. mybatis中#{ } 和 ${ }区别

- #{ }是预编译处理， ${  }是字符串替换
- Mybatis在处理#{  }时，将SQL中的#{ }替换为？号，调用PreparedStatement的set方法来赋值
- Mybatis在处理${  }时，就是把${  }替换成变量的值
- 使用#{ }可以有效防止SQL注入，提高安全性



### 2. mybatis分页方式

- 数组分页
- SQL分页
- 拦截器分页
- RowBounds分页



### 3. mybatis逻辑分页和物理分页区别

- 物理分页速度上并不一定快于逻辑分页，逻辑分页速度上也并不一定快于物理分页
- 物理分页总是优于逻辑分页：没必要将属于数据库端的压力加到应用端来，就算速度上存在优势，然而其性能上的优点足以弥补这个缺点



### 4. mybatis是否支持延迟加载，延迟加载原理

Mybatis仅支持association（一对一）关联对象和collection（一对多查询）关联集合对象的延迟加载。可以配置是否启用延迟加载：`lazyLoadingEnabled=true|false`

原理：使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用 `a.getB().getName()`，拦截器 `invoke()`方法发现 `a.getB()`是null值，那么就会单独发送事先保存好的查询关联B对象的SQL，把B查询上来，然后调用 `a.setB(b)`，于是a的对象b属性就有值了，接着完成 `a.getB().getName()`方法调用。



### 5.mybatis的一级缓存和二级缓存

一级缓存：基于PerpetualCache的HashMap本地缓存，其存储作用域为Session，当Session flush或者close之后，该Session中的所有Cache就将清空，默认打开一级缓存。

二级缓存：默认也是采用PerpetualCache，HashMap存储，存储作用域为Mapper（Namespace），并且可自定义存储源，如Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口（可用来保存对象的状态），可在它的映射文件中配置 `<cache/>`

对于缓存数据更新机制，当某一个作用域（一级缓存Session/二级缓存Namespace）进行了C/U/D操作后，默认该作用域下所有select中的缓存将被clear



### 6. mybatis和hibernate区别

- Mybatis和Hibernate不同，它不完全是一个ORM框架，因为MyBatis需要程序员自己编写SQL语句
- Mybatis直接编写原生态SQL，可以严格控制SQL执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，如果需要实现支持多种数据库的软件，则需要自定义多套SQL映射文件，工作量大
- Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用hibernate开发可以节省很多代码，提高效率



### 7. mybatis有哪些执行器（Executor）

- SimpleExecutor：每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象
- ReuseExecutor：执行update或select，以SQL作为key查找Statement对象，存在就使用，不存在就创建，用完后不关闭Statement对象，而是放置于Map内，供下一次使用
- BatchExecutor：执行update（没有select，JDBC批处理不支持select），将所有SQL都添加到批处理中（`addBatch()`），等待统一执行（`executeBatch()`），它缓存了多个Statement对象，每个Statement对象都是 `addBatch()`完毕后，等待逐一执行 `executeBatch()`批处理。与JDBC批处理相同



### 8. mybatis分页插件实现原理

使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的SQL，然后重写SQL，根据dialect，添加对应的物理分页语句和物理分页参数。



### 9. mybatis如何编写一个自定义插件

Mybatis自定义插件针对Mybatis四大对象（Executor、StatementHandler、ParameterHandler、ResultSetHandler）进行拦截，具体拦截方式为：

- Executor：拦截执行器的方法（log记录）
- StatementHandler：拦截SQL语法构建的处理
- ParameterHandler：拦截参数的处理
- ResultSetHandler：拦截结果集的处理

Mybatis自定义插件必须实现Interceptor接口：

```java
public interface Interceptor {
    Object intercept(Invocation invocation) throws Throwable;
    Object plugin(Object target);
    void setProperties(Properties properties);
}
```

- intercept方法：拦截器具体处理逻辑方法
- plugin方法：根据签名signatureMap生成动态代理对象
- setProperties方法：设置Properties属性

一个自定义插件示例：

```java
// ExamplePlugin.java
@Intercepts({@Signature(
  type= Executor.class,
  method = "update",
  args = {MappedStatement.class,Object.class})})
public class ExamplePlugin implements Interceptor {
  public Object intercept(Invocation invocation) throws Throwable {
  Object target = invocation.getTarget(); //被代理对象
  Method method = invocation.getMethod(); //代理方法
  Object[] args = invocation.getArgs(); //方法参数
  // do something ...... 方法拦截前执行代码块
  Object result = invocation.proceed();
  // do something .......方法拦截后执行代码块
  return result;
  }
  public Object plugin(Object target) {
    return Plugin.wrap(target, this);
  }
  public void setProperties(Properties properties) {
  }
}
```

一个@Intercepts可以配置多个@Signature，@Signature中的参数定义为：

- type：表示拦截的类，这里是Executor的实现类
- method：表示拦截的方法，这里是拦截Executor的update方法
- args：表示方法参数