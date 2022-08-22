## MySQL基础

### 一.数据库概述与MySQL安装

#### 1.数据库概述

>  DB(数据库)：存储数据的仓库，本质是一个文件系统，保存了一系列有组织的数据。

> DBMS(数据库管理系统)：一种操纵和管理数据库的大型软件，用于建立，使用和维护数据库，对数据库进行统一管理和控制。用户通过数据库管理系统访问数据库中表内的数据。

> SQL(结构化查询语言)：专门用来与数据库通信的语言。

关系型数据库：关系型数据库模型把复杂的数据结构归结为简单的二元关系。关系型数据库以行和列的形式存储数据。

非关系型数据库：基于键值对存储数据，不需要经过SQL层的解析，性能非常高。

**常见的非关系型数据库:**

-  键值型数据库
  - 通过Key-Value键值的方式来存储数据。键值型数据库典型的使用场景是作为内存缓存。最流行的键值型数据库是Redis。
- 文档型数据库
  - 文档型数据库可存放并获取文档，可以是XML,JSON等格式。在数据库中文档作为处理信息的基本单位，一个文档就相当于一条记录。最流行的文档型数据库是MongoDB,CouchDB。
- 搜索引擎数据库
  - 核心原理是"倒排索引"。典型产品：Elasticsearch，Solr，Splunk等。
- 列式数据库
  - 列式数据库是将数据按照列存储到数据库中，好处是可以大量降低系统的I/O,适用于分布式文件系统，不足之处在于功能相对有限。典型产品：HBase等。
- 图形数据库
  - 典型产品：Neo4J，InfoGrid等。

NoSQL使用场景：日志收集，排行榜，定时器等。	



> E-R模型中最重要的概念：实体集，属性，联系集。
>
> 表的四种关联关系：一对一关联，一对多关联，多对多关联，自我引用

#### 2.MySQL环境安装

查看编码命令:

> show variables like 'cjaracter_%';
>
> show variables like 'collation_%';

对于MySQL5.7版本来说，字符集默认是latin1,故需要修改My.ini配置文件中的字符集为utf-8。



由于MySQL8.0之前的版本中的加密规则是mysql_native_password,而在MySQL8.0之后，加密规则是caching_sha2_password,所以会出现使用低版本的图形工具连接时会出现连接错误，解决方案有两种:1.升级图形界面工具版本 2.把MySQL8.0用户登录密码加密规则还原成mysql_native_password。

解决方案2的命令:

> USE mysql;
>
> ALTER USER ‘root’@'localhost' IDENTIFIED WITH mysql_native_password BY '密码';
>
> FLUSH PRIVILEGES;

### 二.SQL之select使用

**SQL分类:**

- DDL（Data Definition Languages）：数据定义语言。 CREATE  \  ALTER  \  DROP  \  RENAME  \  TRUNCATE 
- DML（Data Manipulation Languages）：数据操作语言。  INSERT  \ DELETE  \  UPDATE  \  SELECT
- DCL（Data Control Languages）：数据控制语言。 COMMIT  \  ROLLBACK  \  SAVEPOINT  \  GRANT  \  REVOKE

**SQL语言的规则**

- 字符串，日期时间类型的变量需要使用一对' '。
- 列的别名，尽量使用双引号（“ ”），而且不建议省略as。

 **SQL语言的规范**

- MySQL在Windows环境下是大小写不敏感的
- MySQL在Linux环境下是大小写敏感的	
  - 数据库名，表名，表的别名，变量名是严格区分大小写的。
  - 关键字，函数名，列名（或字段名），列的别名（字段的别名）是忽略大小写的。
- 推荐的书写规范：
  - 数据库名，表名，表别名，字段名，字段别名等都小写
  - SQL关键字，函数名，绑定变量等都大写

导入数据表，表中数据的方式:

1. 命令行使用命令：source 文件的全类名
2. 图形化界面工具















