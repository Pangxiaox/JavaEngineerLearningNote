# RabbitMQ

### 1. RabbitMQ使用场景

①跨系统的异步通信，所有需要异步交互的地方都可以用消息队列

②多个应用之间的耦合，由于消息是平台无关和语言无关的，而且语义上也不是函数调用，因此更适合作为多个应用之间松耦合的接口。基于消息队列的耦合，不需要发送方和接收方同时在线。

③应用内的同步变异步。比如订单处理，可以由前端应用将订单信息放到队列，后端应用从队列里依次获得信息处理，高峰时大量订单可以积压在队列里慢慢处理。由于同步通常意味着阻塞，而大量线程的阻塞会降低计算机性能。

④消息驱动的结构，系统分解为消息队列，和消息制造者和消息消费者，一个处理流程可以根据需要拆成多个阶段，阶段之间用队列连接起来，前一个阶段处理的结果放入队列，后一个阶段从队列中获取消息继续处理。

⑤应用需要更灵活的耦合方式，如发布订阅，比如可以指定路由规则

⑥跨局域网，甚至跨城市的通讯



### 2. RabbitMQ重要的角色和重要的组件

**重要的角色**

- 生产者：消息创建者，负责创建和推送数据到消息服务器
- 消费者：消息接收方，处理数据和确认信息
- 代理：RabbitMQ本身，不生产消息，扮演“快递”角色

**重要的组件**

- ConnectionFactory（连接管理器）：应用程序与RabbitMQ之间建立连接的管理器，程序代码中使用
- Channel（信道）：消息推送使用的通道
- Exchange（交换器）：接收、分配消息
- Queue（队列）：存储生产者的消息
- RoutingKey（路由键）：把生产者的数据分配到交换器上
- BindingKey（绑定键）：把交换器的消息绑定到队列上



### 3. RabbitMQ中vhost作用

vhost可理解为虚拟broker，即mini-RabbitMQ server。其内部均含有独立的queue、exchange和binding等，其拥有独立的权限系统，可做到vhost范围的用户控制。

从RabbitMQ全局角度，vhost可作为不同权限隔离的手段（例子：不同的应用可以跑在不同vhost中）



### 4. RabbitMQ消息是怎么发送的

首先客户端必须连接到RabbitMQ服务器才能发布和消费消息，客户端和RabbitMQ server之间会创建一个TCP连接，一旦TCP打开并通过了认证（认证：发送给RabbitMQ server的用户名和密码），你的客户端和RabbitMQ就创建了一条AMQP信道，信道是创建在“真实”TCP上的虚拟连接，AMQP命令都是通过信道发送出去的，每个信道会有一个唯一id，不论是发布信息，订阅队列都是通过这个信道完成。



### 5. RabbitMQ怎么保证消息的稳定性

- 提供事务功能
- 通过将channel设置为confirm（确认）模式



### 6. RabbitMQ怎么避免消息丢失

- 消息持久化
- ACK确认机制
- 设置集群镜像模式
- 消息补偿机制



### 7. 要保证消息持久化成功的条件

- 声明队列必须设置持久化durable为true
- 消息推送投递模式必须设置持久化，deliveryMode设置为2（持久）
- 消息已经到达持久化交换器
- 消息已经到达持久化队列

四个条件都满足才可以保证消息持久化成功



### 8. RabbitMQ持久化的缺点

降低了服务器的吞吐量，因为用的是磁盘而非内存存储，可尽量使用SSD硬盘来缓解吞吐量问题



### 9. RabbitMQ的几种广播类型

- fanout：所有bind到此exchange的queue都可以接收消息（纯广播）
- direct：通过routingKey和exchange决定的那个唯一的queue可以接收消息
- topic：所有符合routingKey（可以是一个表达式）的routingKey所bind的queue可以接收消息



### 10. RabbitMQ怎么实现延迟消息队列

- 通过消息过期后进入死信交换器，再由交换器转发到延迟消费队列，实现延迟功能
- 使用RabbitMQ-delayed-message-exchange插件实现延迟功能



### 11. RabbitMQ集群用途

- 高可用：某个服务器出现问题，整个RabbitMQ还可继续使用
- 高容量：集群可以承载更多的消息量



### 12. RabbitMQ节点类型

- 磁盘节点：消息存储到磁盘
- 内存节点：消息存储在内存，重启服务器消息丢失，性能高于磁盘类型



### 13. RabbitMQ集群搭建需要注意什么问题

- 各节点之间使用“--link”连接，此属性不能忽略
- 各节点使用的erlang cookie值必须相同，此值相当于“秘钥”功能，用于各节点认证
- 整个集群中必须包含一个磁盘节点



### 14. RabbitMQ每个节点是其他节点的完整拷贝吗

不是

- 存储空间考虑：如果每个节点都拥有所有队列的完全拷贝，这样新增节点不但没有新增存储空间，反而增加了更多冗余数据
- 性能考虑：如果每条消息都需要完整拷贝到每一个集群节点，那新增节点并没有提升处理消息的能力，最多是保持和单节点相同的性能甚至更糟



### 15. RabbitMQ集群中唯一一个磁盘节点崩溃了会发生什么情况

- 不能创建队列
- 不能创建交换器
- 不能创建绑定
- 不能添加用户
- 不能更改权限
- 不能添加和删除集群节点

唯一磁盘节点崩溃，集群可以保持运行，但不能修改任何东西



### 16. RabbitMQ对集群节点停止顺序有要求吗

有要求，先关闭内存节点，最后再关闭磁盘节点。如果顺序恰好相反的话，可能会造成消息丢失。