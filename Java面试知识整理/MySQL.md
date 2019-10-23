# MySQL

### 1.  数据库三大范式

- 1NF：要求数据库表的每一列都是不可分割的原子数据项。

- 2NF：在1NF的基础上，非主属性必须完全依赖于候选码（在1NF基础上消除非主属性对主码的部分函数依赖），确保数据库表中的每一列都和主键相关，而不能只与主键的某一部分相关。

  例子：

  student_course(学号，姓名，课程名，学分，成绩)

  （学号，课程名） → （姓名，成绩，学分）

  但姓名仅依赖于学号，学分仅依赖于课程名，不满足2NF

  ▲分解：student（学号，姓名）

  ​				course（课程名，学分）

  ​				student_course(学号，课程名，成绩)

  ⭐2NF不满足带来的问题：

  - 数据冗余

    对于一门课程，如果有n名学生选修，则这门课程的全部信息将会重复存储n-1次，同理，一个学生选修了m门课程，则学生全部信息会重复存储m-1次，导致数据冗余存储。

  - 更新问题（Update Anomaly）

    如果要更新某门课程的学分，那么所有关联这门课程的记录都将更新，否则会出现数据不一致问题。

  - 插入问题（Insert Anomaly）

    假如新增一门课程，但是尚未有学生选修，则该门课程的信息无法入库。

  - 删除问题（Delete Anomaly）

    假如某门课程对应的记录完全被删除，则将导致这门课程的信息完全丢失。

- 3NF：在2NF基础上，任何非主属性不依赖于其它非主属性（在2NF基础上消除传递函数依赖），确保数据表中的每一列数据都和主键直接相关，而不能间接相关。

  例子：

  emp(id，name，age，dept_id，dept_name，dept_detail)

  （id） → （name，age，dept_id，dept_name，dept_detail）

  （dept_id） → （dept_name，dept_detail)

  不满足3NF

  ▲分解：emp(id，name，age，dept_id)

  ​				dept（dept_id，dept_name，dept_detail）



### 2.  如何获取当前数据库版本

```mysql
select version()
```



### 3.  char和varchar的区别

##### 3.1 char

固定长度类型，如char（10），当输入三个字符“abc”时，它们占的空间仍然是10个字节，其他7个是空字节。

优点：效率高

缺点：占用空间

使用场景：存储密码的MD5值

##### 3.2 varchar

可变长度类型，存储了每个值占用的字节再加上一个用来记录其长度的字节的长度

##### 3.3 比较

从空间上考虑varchar更合适；从效率上考虑char更合适



### 4.  float和double的区别

##### 4.1 float

单精度，在内存中占4个字节

##### 4.2 double

双精度，在内存中占8个字节



### 5.  MySQL的内连接、左连接、右连接

内连接是把匹配的关联数据显示出来；左连接是左边的表全部显示出来，右边的表显示出符合条件的数据，缺失值用null表示；右连接与左连接相似。

```mysql
create table testa
(id int primary key,
age int);

insert into testa values(1,22);
insert into testa values(2,21);
insert into testa values(3,26);
insert into testa values(4,30)
```

```mysql
create table testb
(id int primary key,
sex char);

insert into testb values(1,'男');
insert into testb values(2,'女');
```

##### 5.1 内连接

```mysql
select testa.id as testa_id,age,testb.id as testb_id,sex
from testa inner join testb on 
testa.id = testb.id
```

| testa_id | age  | testb_id | sex  |
| -------- | ---- | -------- | ---- |
| 1        | 22   | 1        | 男   |
| 2        | 21   | 2        | 女   |

##### 5.2 左（外）连接

```mysql
select testa.id as testa_id,age,testb.id as testb_id,sex
from testa left outer join testb on 
testa.id = testb.id
```

| testa_id | age  | testb_id | sex  |
| -------- | ---- | -------- | ---- |
| 1        | 22   | 1        | 男   |
| 2        | 21   | 2        | 女   |
| 3        | 26   | null     | null |
| 4        | 30   | null     | null |

##### 5.3 右（外）连接

```mysql
select testa.id as testa_id,age,testb.id as testb_id,sex
from testa right outer join testb on 
testa.id = testb.id
```

| testa_id | age  | testb_id | sex  |
| -------- | ---- | -------- | ---- |
| 1        | 22   | 1        | 男   |
| 2        | 21   | 2        | 女   |



### 6.  数据库ACID

##### 6.1（A）Atomicity：原子性

每个事务中的所有操作要么全部成功，要么全部失败；如果事务中的部分操作失败了，那么整个事务也就失败了。

##### 6.2（C）Consistency：一致性

确保了任何事务都会使数据库从一个合法（一致）的状态变为另一个合法（一致）的状态。

例子：设有五个账户，每个账户余额是100元，那么五个账户总额是500元，如果在这个5个账户之间同时发生多次转账，无论并发多少个，比如在A与B账户之间转账5元，在C与D账户之间转账10元，在B与E之间转账15元，五个账户总额也应该还是500元，

##### 6.3（I）Isolation：隔离性

数据库允许多个并发事务同时对其数据进行读写和修改的能力，多个事务并发访问时，事务之间是隔离的，一个事务不应该影响其它事务运行效果。并发执行的事务彼此无法看到对方的中间状态。

##### 6.4（D）Durability：持久性

事务完成后所做的改动都会被持久化，即使发生灾难性的失败。通过日志和同步备份可以在故障发生后重建数据。在事务完成以后，该事务对数据库所作的更改便持久地保存在数据库之中，并不会被回滚。



### 7.  MySQL中索引是怎么实现的

索引可以帮助我们实现高效查找数据，MySQL中的索引，不用的数据引擎实现略有不同（见下面具体内容），但目前主流的数据库引擎都是B+ 树实现的，和hash、二叉树、红黑树等数据结构相比，B+ 树一大特点就是高度可控，可以实现高效率查找。

##### 7.1 InnoDB

InnoDB数据文件本身就是索引文件，采用聚集索引。

###### 7.1.1 主索引

如图，叶子节点包含了完整数据记录

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/InnoDB_PKIDX.png)

###### 7.1.2 辅助索引

如图，定义在col3上的辅助索引。InnoDB辅助索引的data域存储相应记录主键的值而不是地址。通过辅助索引进行查找分两步：首先查找到辅助索引对应的主键，然后再去聚集索引中获取主键对应的数据。

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/InnoDB_SECIDX.png)

##### 7.2 MyISAM

采用非聚集索引。检索算法首先按照B+ 树搜索索引，如果指定key存在，那么取出其data域的值，然后以data域的值作为地址，读取相应数据记录。

###### 7.2.1 主索引

如图，叶子节点data域存放数据记录的地址

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/MyISAM_PKIDX.png)

###### 7.2.2  辅助索引

如图，在col2建立一个辅助索引，叶子节点data域保存数据记录的地址

![Image text](https://github.com/Pangxiaox/JavaEngineerLearningNote/blob/master/Notes-Pic/MYISAM_SECkey.png)



### 8.  怎么验证MySQL的索引是否满足需求

使用explain查看SQL如何执行查询语句，从而分析索引是否满足需求

```mysql
explain select * from testa where id = 2
```

| id   | select_type | table | partitions | type  | possible_keys | key     | key_len | ref   | rows | filtered | Extra |
| ---- | ----------- | ----- | ---------- | ----- | ------------- | ------- | ------- | ----- | ---- | -------- | ----- |
| 1    | SIMPLE      | testa | null       | const | PRIMARY       | PRIMARY | 4       | const | 1    | 100.00   | null  |

```mysql
create index IDidx on testa(id)

explain select * from testa where id = 2
```

| id   | select_type | table | partitions | type  | possible_keys | key     | key_len | ref   | rows | filtered | Extra |
| ---- | ----------- | ----- | ---------- | ----- | ------------- | ------- | ------- | ----- | ---- | -------- | ----- |
| 1    | SIMPLE      | testa | null       | const | PRIMARY,IDidx | PRIMARY | 4       | const | 1    | 100.00   | null  |



### 9.  数据库的事务隔离

由低到高依次为：Read Uncommited、Read Commited、Repeatable Read、Serializable

##### 9.1 Read Uncommited 未提交读

定义：一个事务可以读取到另一个未提交事务的数据

缺点：造成脏读、不可重复读、幻读

案例：老板要给程序员发工资，程序员的工资是3.6万/月。但是发工资时老板不小心按错了数字，按成3.9万/月，该钱已经打到程序员的户口，但是事务还没有提交，就在这时，程序员去查看自己这个月的工资，发现比往常多了3千元，以为涨工资了非常高兴。但是老板及时发现了不对，马上回滚差点就提交了的事务，将数字改成3.6万再提交。实际程序员这个月的工资还是3.6万，但是程序员看到的是3.9万。他看到的是老板还没提交事务时的数据。这就是脏读。

##### 9.2 Read Commited 提交读

定义：一个事务读取到其他事务提交后的数据，一个事务要等其他事务提交后才能读取数据

缺点：造成不可重复读、幻读

案例：股市升高后，股民老王查看自己持有5000股，就想卖掉4000股，在老王卖股票的时候，老王的老婆看股市太高，就登录老王的账号，卖掉3000股。当老王想卖股票时，发现自己只有2000股，不是之前看到的5000股，这就是不可重复读问题。

▲大多数数据库默认的事务隔离级别（如Oracle、但不包括MySQL）

##### 9.3 Repeatable Read 可重复读

定义：一个事务对同一份数据读取到的相同，不在乎其他事务对数据的修改，开始读取数据（事务开启）时，不再允许修改操作

缺点：造成幻读

案例：股市忽涨忽跌，股民老王焦虑不安，按捺不住，想把持有的多种股票全部抛掉。与此同时，老外老婆听信砖家所言，使用老王的账号买了某只神股。老王抛掉所有股票后，查看自己的持股，猛然发现自己居然还持有一只股票，瞬间觉得一脸懵逼，这就是幻读导致。

⭐MySQL默认的事务隔离级别

##### 9.4 Serializable 可串行化

定义：事务串行化执行

缺点：能解决脏读、不可重复读和幻读问题，但效率低下

#### 关于脏读、不可重复读和幻读

- 脏读：读到了别的事务回滚前的脏数据。比如事务B执行过程中修改了数据X，在未提交前，事务A读取了X，而事务B却回滚了，这样事务A就形成了脏读。

  ▲当前事务读到的数据是别的事务想要修改成为的但没有修改成功的数据

- 不可重复读：事务A首先读取了一条数据，然后执行逻辑的时候，事务B将这条数据改变了，然后事务A再次读取的时候，发现数据不匹配。

  ▲当前事务先进行了一次数据读取，然后再次读取到的数据是别的事务修改成功的数据，导致两次读取到的数据不匹配

  ⭐产生的主要原因是数据的更新

- 幻读：事务A首先根据条件索引得到N条数据，然后事务B改变了这N条数据之外的M条或者增添了M条符合事务A搜索条件的数据，导致事务A再次搜索发现有N+M条数据了，就产生了幻读。

  ▲当前事务读取第一次获得的数据和后来读取到的数据条目不一致

  ⭐产生的主要原因是数据的新增或删除



### 10.  MySQL常用的存储引擎

##### 10.1 InnoDB引擎

InnoDB是事务型数据库首选引擎，支持ACID，支持行级锁和外键，支持崩溃修复能力和并发控制。Ver 5.5之后成为MySQL默认引擎。在InnoDB中存在缓冲管理，通过缓冲池将索引和数据全部缓存起来，加快查询速度。适用于对事务完整性要求较高，要求实现并发控制的场景以及需要频繁更新和删除操作的数据库。

##### 10.2 MyISAM引擎

MyISAM不支持事务，不支持外键，默认的锁粒度是表级锁，并发度很差。Ver 5.5之前是MySQL的默认引擎。插入数据快，空间和内存使用比较低。如果表主要是用于插入新记录和读出记录，那么选择MyISAM能实现处理高效率。如果应用的完整性、并发性要求比较低，也可以使用

##### 10.3 MEMORY引擎

MEMORY不支持事务，不支持外键，支持的锁粒度是表级锁。所有的数据都在内存中，数据的处理速度快，但是安全性不高。如果需要很快的读写速度，对数据的安全性要求较低，可以选择MEMOEY。它对表的大小有要求，不能建立太大的表。所以，这类数据库只使用在相对较小的数据库表

##### 10.4 综合分析

- 一个表要求比较高的事务处理，选择InnoDB引擎
- 将查询要求比较高的表选择MyISAM存储引擎
- 如果数据库需要一个用于查询的临时表，选择MEMORY存储引擎



### 11.  MySQL的行锁和表锁

MyISAM只支持表锁，InnoDB支持表锁和行锁，默认为行锁。

- 表级锁：

  优势：开销小，加锁快，不会出现死锁。

  劣势：锁定粒度大，发生锁冲突的概率最高，并发量最低。

- 行级锁：

  优势：开销大，加锁慢，会出现死锁。

  劣势：锁定粒度小，发生锁冲突的概率小，并发度最高。



### 12.  MySQL的乐观锁和悲观锁

- 乐观锁：每次拿数据时都认为别人不会修改，所以不会上锁，但是在提交更新时会判断一下在此期间别人有没有去更新这个数据。
- 悲观锁：每次拿数据时都认为别人会修改，所以每次拿数据时都会上锁，这样别人想拿这个数据就会阻止，直到这个锁被释放。

▲数据库本身已经实现了悲观锁，而数据库的乐观锁需要自己实现，在表里加一个version字段，每次修改成功值加1，这样每次修改的时候先对比一下，自己拥有的version和数据库现在的version是否一致，如果不一致就不修改，这样就实现了乐观锁。



### 13.  MySQL问题排查有哪些方法

- 使用 `show processlist`命令查看当前所有连接信息。
- 使用 `explain`命令查询SQL语句执行计划。
- 开启慢查询日志，查看慢查询的SQL。



### 14.  如何做MySQL的性能优化

- 为搜索字段创建索引。
- 避免使用 `select *`，应该列出需要查询的字段。
- 垂直分割分表
- 选择正确的存储引擎



### 15. MySQL的共享锁和排他锁

MySQL行级锁中包含有共享锁和排他锁。

- 共享锁（S锁）：读锁，多个事务对于同一数据可以共享一把锁，都能访问到数据，但是只能读不能修改。
- 排他锁（X锁）：写锁，排他锁就是不能与其他锁并存，如果一个事务获取了一个数据行的排他锁，其他事务就不能再获取该行的其他锁，包括共享锁和排他锁，但是获取到排他锁的事务可以对数据行进行读取和修改。

▲MySQL InnoDB引擎默认的修改语句，如update、delete、insert都会自动给涉及到的数据加上排他锁，select语句默认不会加任何锁类型。

▲加锁方式：

共享锁： `select ... lock in share mode`

排他锁： `select ... for update`



### 16.  InnoDB和MyISAM综合对比

| 比较条目 | InnoDB                     | MyISAM     |
| -------- | -------------------------- | ---------- |
| 外键     | 支持                       | 不支持     |
| 事务     | 支持                       | 不支持     |
| 锁       | 支持行锁、表锁（默认行锁） | 仅支持表锁 |
| 索引     | 聚集索引                   | 非聚集索引 |



### 17. MySQL分库分表分区

##### 17.1 分库

把一个大型的数据库分成若干个小型的数据库



##### 17.2 分表

```mysql
create table total
(id int primary key,
age int)
engine = MyISAM

create table table1
(
id int primary key,
age int
)engine = MyISAM;

create table table2 like table1

create table tb_total
(
id int primary key,
age int
)
ENGINE = MERGE UNION = (table1,table2) INSERT_METHOD = LAST;
```

```mysql
insert into total values(1, 20);
insert into total values(2, 21);
insert into total values(3, 23);
insert into total values(4, 25);
insert into total values(5, 24);

insert into table1(id,age) 
select id,age from total where id%2 =0;

insert into table2(id,age)
select id,age from total where id%2 =1;
```

- table1结果：

  | id   | age  |
  | ---- | ---- |
  | 2    | 21   |
  | 4    | 25   |

- table2结果：

  | id   | age  |
  | ---- | ---- |
  | 1    | 20   |
  | 3    | 23   |
  | 5    | 24   |



##### 17.3 分区

```mysql
create table test
(id int primary key,
age int)
partition by RANGE (id)
(
partition p0 values less than (2),
partition p1 values less than (5),
partition p2 values less than (10)
)
```

```mysql
insert into test values (1,20);
insert into test values (2,20);
insert into test values (3,26);
insert into test values (4,24);
insert into test values (5,25);
insert into test values (6,22);
insert into test values (7,20);
insert into test values (8,20);
insert into test values (9,21);
```

```mysql
select 
    partition_name,
    table_rows
from information_schema.`PARTITIONS` where table_name = 'test';
```

| PARTITION_NAME | TABLE_ROWS |
| -------------- | ---------- |
| p0             | 1          |
| p1             | 3          |
| p2             | 5          |

