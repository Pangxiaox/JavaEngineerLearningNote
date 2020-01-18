# Java Web

### ⭐1. JSP和Servlet的区别

- JSP经过编译后就变成Servlet
- JSP更擅长于页面显示，Servlet更擅长于逻辑控制
- Servlet中没有内置对象，JSP中的内置对象都必须通过HttpServletRequest对象，HttpServletResponse对象以及HttpServlet对象得到
- JSP是Servlet的一种简化，使用JSP只需要完成程序员需要输出到客户端的内容，JSP中的Java脚本如何镶嵌到一个类中则由JSP容器完成。Servlet则是完整的Java类，这个类的Service方法用于生成对客户端的响应



### 2. JSP有哪些内置对象，作用分别是什么

| 内置对象    | 描述                                              |
| ----------- | ------------------------------------------------- |
| request     | 封装客户端的请求，其中包含来自GET或POST请求的参数 |
| response    | 封装服务器对客户端的响应                          |
| pageContext | 通过该对象可以获取其他对象                        |
| session     | 封装用户会话的对象                                |
| application | 封装服务器运行环境的对象                          |
| out         | 输出服务器响应的输出流对象                        |
| config      | Web应用的配置对象                                 |
| page        | JSP页面本身                                       |
| exception   | 封装页面抛出异常的对象                            |



### 3. JSP的4种作用域

| 作用域      | 描述                                             |
| ----------- | ------------------------------------------------ |
| page        | 与一个页面相关的对象和属性                       |
| request     | 与Web客户机发出的一个请求相关的对象和属性        |
| session     | 与某个用户与服务器建立的一次会话相关的对象和属性 |
| application | 与整个Web应用程序相关的对象和属性                |



### ⭐4. session和cookie的区别

session是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；cookie是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现session的一种方式



### ⭐5. session的工作原理

session是一个存放在服务器上类似于散列表格的文件，里面存有我们需要的信息，在我们需要时可以从里面取出来。session本身是个map集合，键存储的是sessionid，用户向服务器发送请求时会带上这个sessionid，这时就可以从中取出对应值。



### 6. 如果客户端禁用cookie，那么session还能用吗

禁用了cookie就不能得到session

因为session是用session ID来确定当前对话所对应的服务器Session，而Session是通过cookie传递的，禁用cookie相当于失去了Session ID



### 7. Spring MVC和Struts的区别

- 拦截机制不同

Struts2是类级别的拦截，SpringMVC是方法级别的拦截。

Struts2有自己的拦截Interceptor机制，Spring MVC是用独立的AOP方式，这样导致Struts2的配置文件量比Spring MVC大

- 底层框架不同

Struts2采用Filter实现，Spring MVC则用Servlet实现。

- 性能不同

Spring MVC开发效率和性能高于Struts2

- 配置方面

Spring MVC和Spring是无缝的，从项目管理和安全层面也比Struts2高



### ⭐8. 如何避免SQL注入

SQL注入：一种攻击手段，通过把非法的SQL命令插入到Web表单中或页面请求查询字符串中，最终达到欺骗服务器执行恶意的SQL语句的目的。

避免SQL注入的方式：

- PreparedStatement（简单有效）
- 使用正则表达式过滤传入的参数
- 字符串过滤
- JSP中调用该函数检查是否包含非法字符
- JSP页面判断代码



### 9. 什么是XSS攻击，如何避免

XSS攻击，又称为CSS（Cross Site Script），即跨站脚本攻击，指攻击者向有XSS漏洞的网站中输入恶意的HTML代码，当用户浏览该网站时，这段HTML代码会自动执行，从而达到攻击目的。XSS是Web程序中常见漏洞，XSS属于被动式且用于客户端的攻击方式。

防范思路：对输入（和URL参数）进行过滤，对输出进行编码。



### 10. 什么是CSRF攻击，如何避免

CSRF攻击（Cross-site request forgery），也被称为one-click attack或者session riding，中文是跨站请求伪造。攻击者通过伪造用户的浏览器的请求，向访问一个用户自己曾经认证访问过的网站发送出去，使目标网站接收并误以为是用户的真实操作而去执行命令。常用于盗取账号、转账、发送虚假消息等。

防范思路：

- 验证HTTP Referer字段
- 使用验证码
- 在请求地址中添加token并验证
- 在HTTP头中自定义属性并验证