## MySQL高级

### 一.Linux上MySQL的卸载，安装与使用

#### 1. MySQL的卸载步骤

1. 关闭MySQL服务
   - systemctl stop mysqld.service
2. 查看Linux上是否有MySQL的安装包
   - rpm -qa | grep -i mysql
3. 使用yum remove 删除安装程序
   - yum remove  安装程序名
4. 通过命令查找MySQL的所有文件
   - find  /  -name mysql
5. 使用命令删除MySQL相关文件
   - rm -rf 文件名
6.  	删除MySQL的配置文件
   - rm -rf /etc/my.cof

#### 2.Linux上MySQL的安装

1. 给/tmp文件夹设置一个比较大的权限

   - chmod -R 777 /tmp

2. 使用rpm安装MySQL程序

   - rpm -ivh RPM包名

3. 初始化root账户

   - mysqld --initialize --user=mysql

4. 查看root账户密码

   cat /var/log/mysqld.log

5. 使用systemctl start mysql.service启动mysql服务
6.  进入MySQL 修改root账户密码

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '{pwd12345}';
```

```sql
flush privileges;
```

### 二.MySQL的数据目录

#### 2.1 linux上字符集的修改与底层原理

1. 进入 /etc/my.cnf文件中 添加 character_set_server = utf8
2. 重启mysql服务 ： systemctl restart mysqld.service

字符集有四个级别:

- 服务器级别
- 数据库级别
- 表级别
- 列级别

>  如果下一个级别没有显示指定字符集，默认使用上一级别的字符集。

比较规则

> utf8_unicode_ci和utf8_general_ci对中，英文来说没有实质的区别
>
> utf8_general_ci校对速度快，但准确度稍差
>
> utf8_unicode_ci准确度高，但校对速度稍慢
>
> 一般情况下，使用utf8_general_ci就可以，如果出现法语，德语和俄语，就要使用utf8_unicode_ci

#### 2.2 SQL大小写规范

MySQL在linux中

- 数据库名，表名，表的别名，变量名严格区分大小写
- 关键字，函数名不区分大小写
- 列名，列的别名在任何情况下都不区分大小写

#### 2.3 目录

- 数据目录； /var/lib/mysql

- 命令目录：/usr/bin ,   /usr/sbin

- 配置文件目录： /etc/my.cnf  ,   /usr/share/mysql-8.0(或 /usr/share/mysql)

#### 2.4 MySQL下默认的库的说明

- mysql：存储了MySQL的用户账户和权限信息，一些存储过程，事件的定义信息，运行过程中产生的日志信息等
- information_schema：存储基础的表，视图，触发器，列，索引等信息
- performance_schema：保存MySQL运行过程中一些状态信息，用来监控M]ySQL服务的各类性能指标
- sys：通过视图的方式把information_schema和performance_schema结合起来



> /var/lib/mysql/ibtmp1 中存储系统表空间,可以存储表数据，默认大小为12M

**解析ibd文件命令**

```
ibd2sdi --dump-file=新文件名.txt  要解析的ibd文件名
```

### 三.用户与权限管理

#### 3.1 用户管理

##### 3.1.1 创建用户

```
create user 用户名 identified by 密码;
```

##### 3.1.2 修改用户

```
update mysql.user set  ...  where user = ?,host = ?;
flush privileges;
```

##### 3.1.3 删除用户

方式一:（推荐使用）

```
drop user 用户名@host名;
```

方式二:

```
delete from mysql.user where host = ? and use = ?;
```

> 该方式不推荐使用，因为可能系统会有残余信息保留，而drop user 则会删除用户信息及对应的权限信息

##### 3.1.4 设置当前用户密码

方式一：使用alter user 对当前用户密码进行修改

```
alter user user() identified by '新密码';
```

方式二:

```
set password = '新密码';
```

当然了，在MySQL5.7当中可以这样操作（不过不建议这样使用，在MySQL8.0中PASSWORD函数被删除了）

```
set password = PASSWORD('新密码');
```

##### 3.1.5 修改其他用户密码

在root账号中对其他用户的密码进行修改

方式一:

```
alter user 用户名@host名 identified by '新密码';
```

方式二:

```
set password for 用户名@host名='新密码';
```

方式三：(不推荐使用)

```
update mysql.user set authentication_string=PASSWORD('新密码') where user=? and host=?;
```

#### 3.2 权限管理

查看权限列表:

```
show grants;
```

##### 3.2.1 授予权限

```
grant 权限1,权限2，... ,权限n on 数据库名.表名 to 用户名@host名 [identified by ‘密码’];
```

赋予所有的权限的命令:

```
grant all privileges on *.* to 用户名@host名;
```

##### 3.2.2 回收权限

```sql
revoke 权限1,权限2, ... ,权限n on 数据库名.表名 from 用户名@host名；
```

##### 3.2.3 权限表

在MySQL中，用户的权限信息存储在user，db，tables_priv，columns_priv，procs_priv等数据表中，当MySQL启动时，MySQL服务器会将这些表中的权限信息加载内存当中。

#### 3.3 用户管理

在MySQL8.0中，新添加了用户管理功能，用来管理具有相同权限的用户，角色是权限的集合。

##### 3.3.1 新建角色

```sql
create role 'role名'@'host名';
```

##### 3.3.2 给角色赋予权限

```sql
grant select,delete ... on 库名.表名 to '角色名‘
```

##### 3.3.3 回收角色

```sql
drop role '角色名';
```

##### 3.3.4 给用户赋予角色

给用户赋予角色后，角色还不起作用。要在角色已经创建并授权后，并且激活了用户的角色，用户的角色才能起作用

```sql
grant '角色名' to '用户名';
```

##### 3.3.5 查看当前用户的角色

```sql
select current_role(); 
```

##### 3.3.6 激活用户角色

方式一:

```sql
set default role '角色名' to '用户名';
```

方式二:将activate_all_roles_on_login设置为ON

```sql
show variables like 'activate_all_roles_on_login';
set global activate_all_roles_on_login = ON;
```

#####  3.3.7 回收角色

```sql
revoke role from '用户名'；
```

### 四.逻辑架构

#### 4.1 逻辑架构

![image-20221008155413280](.\图片\image-20221008155413280.png)

![image-20221008162458389](E:\TyporaNotes\MySQL\图片\image-20221008162458389.png)

MySQL的逻辑架构分为三层，分别为**连接层**，**服务层**，**引擎层**。而l连接层中包括TCP连接池和线程池，服务层又包括SQL接口，解析器，优化器，缓存和缓冲池。

其中，SQL接口的作用是接收用户的SQL命令，并且返回用户需要查询的结果。

> MySQL服务器中有专门的TCP连接池限制连接数，采用长连接模式复用TCP连接。

#### 4.2 SQL执行流程

![image-20221008170824805](.\图片\image-20221008170824805.png)

> 在MySQL5.7中，可以使用命令 
>
> ```sql
> show variables like '%query_cache_type%';
> ```
>
> 来查看缓存是否开启，而在MySQL8.0中，将缓存区给删除了
>
> 使用命令
>
> ```sql
> show global status like 'Qcache%';
> ```
>
> 来查看查询缓存的其他一些信息

**解析器**负责对SQL语句的词法和语法进行分析，**生成语法分析树。**

**优化器**进行SQL查询优化，**生成查询计划**。而SQL查询优化包括逻辑查询优化和物理查询优化。

- 逻辑查询优化：通过**SQL等价变换**提升查询效率
- 物理查询优化：通过索引和表连接方式等技术来进行优化

 ##### 4.2.1 查看SQL执行流程

查看profiling是否开启

```sql
show variables like '%profiling';
```

开启profililng 

```sql
set profiling=ON;
```

查看执行计划:

```sql
show profiles;
```

查询指定的执行计划:

```sql
show profiles for query 执行计划的id值;
```

##### 4.2.2 数据库缓冲池

> InnoDB存储引擎是以页为单位来管理存储空间的，DBMS会申请一块内存来作为数据库的缓冲池。这样做的好处是可以让磁盘的活动最小化，从而减少与磁盘直接进行I/O的时间。

![image-20221009155939334](.\图片\image-20221009155939334.png)

注意：查询缓存和缓冲池不是同一个东西，他们存储的内容不同，缓冲池中存放的是数据页，而查询缓存中存放的是查询SQL以及查询SQL执行后的结构，MySQL8.0 废弃的是查询缓存。

**查询缓冲池大小**

如果使用的是MyISAM存储引擎，它只缓存索引，不缓存数据，对应的键缓存参数为key_buffer_size

如果使用的是InnoDB存储引擎，则使用如下命令:

```sql
show variables like 'innodb_buffer_pool_size';
```

**设置缓冲池大小**

方式一:

```sql
set global innodb_buffer_pool_size = 缓冲池大小;
```

方式二 :在配置问题my.ini 或my.cnf文件中进行如下配置

```sql
[server]
innodb_buffer_pool_size = 缓冲池大小;
```

### 五.存储引擎

查看所有的存储引擎:

```sql
show engines;
```

查看当前使用的存储引擎:

```sql
show variables like '%storage_engines%';
```

修改当前使用的存储引擎:
```sql
set default_storage_engine = 存储引擎名; 
```

#### 5.1 InnoDB存储引擎：具有外键支持的事务性存储引擎

- 除了增加和查询外，还需要更新，删除操作，优先选择使用InnoDB存储引擎
- InnoDB存储引擎是为处理巨大数据量的最大性能设计
- 对比MyISAM存储引擎，InnoDB存储引擎**写处理**效率较差一些
- MyISAM存储引擎只缓存索引，不缓存真实数据,但InnoDB存储引擎不仅缓存索引，还存储真实数据，对内存的要求比较高

- InnoDB存储引擎采用行级锁，而MyISAM存储引擎采用表级锁，在高并发的环境下，InnoDB存储引擎的效率比较高

> NDB存储引擎：MySQL集群专用的存储引擎

### 六.索引的数据结构

**索引的优点：**

- 加快查询速度，**减少与磁盘进行I/O的次数**
- 通过创建唯一索引，可以保证数据的唯一性
- 在使用分组和排序子句时，可以减少分组和排序所花的时间
- 在实现数据的参考完整性方面，可以加速表和表之间的连接。

**索引的缺点：**

- 创建索引和维护索引需要花费一定的时间，而且随着数据量的增加，维护索引所花费的时间也在增加
- 索引需要占用一定的磁盘空间
- 索引可以大大提高查询速度，但一定程度上减慢了新增，删除，更新的速度。因为新增，删除和更新数据后，需要对B+树进行调整。

> 数据页的大小为16KB，因此不建议使用过长的字段作为主键，因为这样会导致一个数据页中存放的数据条数减少，从而导致B+树的层数增加，进而增加与磁盘进行I/O的次数。

#### 6.1 常见的索引概念

索引分为**聚簇索引**和**非聚簇索引**。非聚簇索引又包括联合索引。

聚簇索引不是一种单独的索引类型，而是一种数据存储方式，即所有的用户数据都存储在叶子节点上。所以，在InnoDB存储引擎下，可以说“索引即数据，数据即索引”。

> 聚簇：数据行和相邻的键值聚餐的存储在一起。

- 数据物理存储排序的方式只能有一种，所以每个MySQL的表只能有一个聚簇索引，一般情况下就是该表的主键。
- 如果没有定义主键，InnoDB会选择非空的唯一索引替代
- **InnoDB存储引擎支持聚簇索引，而MyISAM存储引擎不支持聚簇索引**

补充说明:

> 1. 对于二级索引的内节点的目录项记录的内容实际上是由索引列的值，主键值和页号三部分组成，以此来保证内节点的目录记录的唯一性。
> 2. 根页面节点的位置是恒定不变的，在需要创建新的一层索引时，是将根页面节点的数据复制出来创建一个节点，根页面节点的位置保持不变，但存储的内容发生了改变。
> 3. 一个页面最少可以存储两条记录

#### 6.2 MyISAM存储引擎中索引的相关知识和原理

MyISAM存储引擎使用B+Tree作为索引结构，叶子节点的data域存放的是数据记录的地址。 

MyISAM存储引擎中使用的都是二级索引，即非聚簇索引，没有所谓的聚簇索引。

#### 6.3 数据库底层使用的数据结构的优势

> InnoDB本身不支持Hash索引，但是提供自适应Hash索引。
>
> - 如果某个数据经常被访问，当满足一定条件的时候，就会将这个数据页的地址存放到Hash表中。
> - 查看自适应索引是否开启的命令：show variables like  ' %adaptive_hash_index%';

**B+树和B树的区别：**

- B+树有k个关键字就又k个孩子节点；B树有k个关键字就有k+1个孩子节点

- B+树中所有的数据都保存在叶子节点中，并且所有数据构成一个有序链表；而B树中数据保存在叶子节点和非叶子节点中

- B树非叶子节点也会存储数据，所以B树的层数较B+树来说要多，故在查询效率上B+树要优于B树，即B+树进行I/O的次数要少于B树进行I/O的次数

### 七.InnoDB存储引擎的存储结构

>  磁盘与内存进行交互的基本单位是页，在InnoDB存储引擎中默认页的大小为16KB。

查看当前数据库的存储引擎的页的大小的命令：

```sql
show variables like '%innodb_page_size%';
```

![image-20221012143836136](.\图片\image-20221012143836136.png)

- 表空间从管理上可以划分为系统表空间，用户表空间，撤销表空间，临时表空间等。
- 段是数据库中的分配单位，不同类型的数据库对象以不同的段形式存在。
- 区在文件系统是一个连续分配的空间，一个区会分配64个连续的页，一个页的带下为1MB。









































































