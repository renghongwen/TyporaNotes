## JDBC

JDBC（Java Database Connectivity）:独立于特定数据库管理系统，通用的SQL数据库存取和操作的公共接口。

> ODBC(Open Database Connectivity,开放式数据库连接)，是微软在Windows平台下推出的。使用者在程序中只需要调用ODBC Api，由ODBC驱动程序将调用转换成为特定的数据库的调用请求。

### 一.获取数据库连接

Jdbs Url：用于标识一个被注册的驱动程序，驱动程序管理器通过这个Url选择正确的驱动程序，从而建立到数据库的连接。

Jdbc Url的标准由三部分组成，各部分用冒号分隔。   Jdbc:子协议:子名称

```java
@Test
public void testConnection() throws IOException, ClassNotFoundException, SQLException {
    InputStream inputStream = ConnectionTest.class.getClassLoader().getResourceAsStream("jdbc.properties");

    Properties info = new Properties();
    info.load(inputStream);

    String driverClass = info.getProperty("driverClass");
    String url = info.getProperty("url");
    String user = "root";
    String password = "9809236818";

    Class.forName(driverClass);
    Connection connection = DriverManager.getConnection(url, user, password);
    System.out.println(connection);
}
```

### 二.使用PreparedStatement实现数据库操作

一个数据库连接就是一个Socket连接。

对数据库的调用的三种不同的方式：

- Statement：用于执行静态SQL语句并返回它所生成结果的对象。
- PreparedStatement：SQL语句被预编译并存储在此对象中，可以使用此对象多次高效地执行此语句。
- CallableStatement：用于执行SQL存储过程

使用Statement操作数据存在的弊端：

- 存在拼串操作，繁琐
- 存在sql注入问题

> 可以通过获取结果集的元数据来获取查询结果集的列数和列名（或列的别名）。

preparedstatement的好处：

- 解决了Statement的拼串和sql注入问题
- PreparedStatement可以操作Blob类型的数据，而Statement不可以
- PreparedStatement可以实现更高效的批量操作
- PreparedStatement能最大可能提高性能：
  - DBServer会对预编译语句提供性能优化，sql语句在被DBServer的编译器被编译后的执行代码被缓存下来，下次调用时只要是相同的预编译语句就不需要编译，只要将参数直接传入编译过的语句执行代码中就会得到执行
  - 在Statement语句中，即使是相同操作因为数据内容不一样，所以整个语句本身不能匹配

- 语法检查，语义检查，翻译成二进制命令，缓存

### 三.操作blob类型的数据

![image-20221004102111717](./图片/image-20221004102111717.png)

- 如果存储的文件过大，数据库的性能就会下降
- 指定了相关的Blob类型以后，还需要修改my.ini文件，添加上max_allowed_packet=16M,重启mysql服务器，才能达到对应字段的最大储存容量。

### 四.使用PreparedStatement进行批量操作

> mysql服务器默认是关闭批处理的，需要通过一个参数，让mysql开启批处理的支持。在配置文件的url后面添 加  ?rewriteBatchedStatements=true

此外，还可以通过手动提交事务的操作来减少操作的时间

### 五.数据库事务

#### 5.1 事务的基本概念

> 事务：一组逻辑操作单元，使数据从一种状态变换到另外一种状态。
>
> - 一组逻辑操作单元：一个或多个DML操作

**在执行close()方法前，建议恢复自动提交状态，尤其是在使用数据库连接池技术时。**

#### 5.2 事务的ACID属性

- 原子性（Atomicity）
  - 事务中的操作要么都发生，要么都不发生
- 一致性（Consistency）
  - 事务必须使数据库从一个一致性状态变换到另外一个一致性状态中去。
- 隔离性（Isolation）
  - 一个事务的执行不能被其他事务干扰，即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰
- 持久性（Durability）
  - 一个事务一旦被提交，它对数据库中数据对改变就是永久性的

#### 5.3 数据库的并发问题

对于同时运行的多个事务，当这些事务访问数据库中相同的数据时，如果没有采取必要的隔离机制，就会导致各种并发问题：

- 脏读：对于两个事务T1，T2读取来已经**被T2更新但还没有提交的字段**。之后，若T2进行了回滚，T1读取的内容就是临时且无效的。
- 不可重复读：对于两个事务T1,T2，T1读取了一个字段，然后**T2更新了该字段**。之后，T1再次读取同一个字段，值就不同了。
- 幻读：对于两个事务T1,T2，T1从一个表中读取了一个字段，然后T2在该表中插入了一些新的行。之后，如果T1再次读取同一个表，就会多出几行。

#### 5.4 四种隔离级别

| 隔离级别                         | 描述                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| read uncommitted（读未提交数据） | 允许事务读取未被其他事务提交的变更，**脏读，不可重复读和幻读读问题都会出现** |
| read committed（读已提交数据）   | 只允许事务读取已经被其他事务提交的变更，**可以避免脏读**，但不可重复读和幻读问题仍然可能出现 |
| Repeatable read（可重复读）      | 确保事务可以多次从一个字段中读取相同的值。在这个事务持续期间，禁止其他事务对这个字段进行更新，**可以避免脏读和不可重复读**，但幻读的问题仍然存在 |
| serializable（串行化）           | 确保事务可以从一个表中读取相同的行，在这个事务持续期间，禁止其他事务对该表执行插入，更新和删除操作，所有并发问题都可以避免，但性能十分低下 |

> 事务的隔离级别越高，数据一致性就越好，但并发性越弱。
>
> MySQL默认的事务隔离级别为repeatable read。

#### 5.5 设置MySQL的事务隔离级别

查看当前事务的隔离级别

```sql
select @@tx_isolation;
```

设置当前MySQL连接的隔离级别：

```sql
set transaction isolation level read committed;
```

设置全局MySQL连接的隔离级别：

```sql
set global transaction isolation level read committed;
```

### 六.数据库连接池

#### 6.1 开源数据库种类

- DBCP：Apache提供的数据库连接池，tomcat服务器自带dbcp数据库连接池，速度相对c3p0较快
- C3P0：速度相对较慢，稳定性还可以
- Proxool：；有监控连接池状态功能，稳定性较c3p0差一点
- BoneCP：速度快
- Druid：阿里巴巴开源数据库，集DBCP，C3P0，Proxool优点于一身的数据库连接池

#### 6.2 数据库连接池基本配置属性

- 初始连接数
- 最大连接数
- 最小连接数
- 获取连接时最大等待时间
- 最小空闲连接数
- 最大空闲连接数







   























