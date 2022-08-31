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

> show variables like 'character_%';
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

#### 2.1 SQL分类及其规范

SQL分类:

- DDL（Data Definition Languages）：数据定义语言。 CREATE  \  ALTER  \  DROP  \  RENAME  \  TRUNCATE 
- DML（Data Manipulation Languages）：数据操作语言。  INSERT  \ DELETE  \  UPDATE  \  SELECT
- DCL（Data Control Languages）：数据控制语言。 COMMIT  \  ROLLBACK  \  SAVEPOINT  \  GRANT  \  REVOKE

**SQL语言的规则**

- 字符串，日期时间类型的变量需要使用一对' '。
- **列的别名，尽量使用双引号（“ ”），而且不建议省略as。**

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

***



列的去重： 在列名前添加DISTINCT关键字,DISTINCT关键字只能放在首个列名前。

空值参与运算时，结果为Null,此时需要引入IFNULL函数进行处理。

显示表结构：使用命令DESCRIBE或DES

***



#### 2.2 运算符

1. 在SQL中，+没有连接的作用，就表示加法运算。会将字符串转换成数值（隐式转换），转换失败，就按0计算。

2. 安全等于运算符（<=>）和等于运算符的作用是相似的，唯一的区别是 <=> 可以用来对NULL进行判断。在两个操作数均为NULL时，其返回值为1而不为NULL，当一个操作数为NULL时，其返回值为0，而不为NULL。
3. 

![image-20220823223824387](E:\TyporaNotes\MySQL\图片\image-20220823223824387.png)

> %：代表零个或多个不确定的字符
>
> _ ： 代表一个不确定的字符

4. OR可以和AND一起使用，并且AND的优先级高于OR。



#### 2.3 排序与分页 

排序：使用 ORDER  BY  字段名   ASC/DESC  

**列的别名只能在 ORDER BY 中使用，不能在WHERE中使用。**

分页： "LIMIT  偏移量,条目数"

MySQL8.0分页新特性: LIMIT 条目数 OFFSET 偏移量 



#### 2.4 多表查询

> 从sql优化的角度来看，建议在多表查询时，每个字段都指明其所在的表。

***给列起别名和给表起别名的方式不一样***

给列起别名：字段名 as "别名"

给表起别名：表名 表的别名

注意:

- 一旦给表起了别名的话，在SELECT和WHERE中使用到了表名，就一定要使用表的别名，而不能再使用表的原名称

多表查询的分类:

- 角度一：等值连接 | 非等值连接
- 角度二：自连接  |  非自连接         ( 自连接使用的自身的两张表)
- 角度三：内连接 | 外连接

> 内连接：合并具有同一列的两个以上的表的行，结果集中不包含一个表与另一个表不匹配的行
>
> 外连接：两个表在连接过程中除了返回满足连接条件的行以外还返回左（或右）表中不满足条件的行，这种连接被称为左（或右）外连接。没有匹配的行时，结果表中相应的列为NULL。

SQL99语法实现内连接:

​	SELECT ...  FROM ... (INNER)   JOIN  ...  ON ... (JOIN  ...  ON ...  )

SQL 99语法实现外连接:

​	SELECT ... FROM  ... LEFT(RIGHT) JOIN ... ON ...

满外连接:(但MySQL不支持该方式,需要借助UNION关键字来实现)

​	SELCT ... FROM ... FULL JOIN ... ON ...



**UNION的使用**

> 使用UNION关键字，可以给出多条SELECT语句，并将它们的结果组合成单个结果集。**合并时，两个表对应的列数和数据类型必须相同，并且相互对应。**各个SELECT语句之间使用UNION或UNION ALL关键字分隔。

注意：执行UNION ALL语句时所需要的资源比UNION语句少。如果明确知道合并数据后的结果数据不存在重复数据，或者不需要去除重复数据，则尽量使用UNION ALL语句，以提高数据查询的效率。

![image-20220828094101829](E:\TyporaNotes\MySQL\图片\image-20220828094101829.png)

SQL99新特性 - 自然连接

> NATURAL JOIN ：相等于等值连接，它会自动查询两张表中**所有相同的字段**，然后进行等值连接。
>
> 该方式较传统的等值连接更不灵活，建议使用传统的等值连接。

SQL99新特性 - USING 

​	此新特性是对上面的新特性进行的改进，指定了等值连接时具体的查询表的相同字段的名称。

 	例: SELECT  ... FROM ... JOIN ... ON .... USING (相同字段名称)

《阿里巴巴Java开发手册》

强制：超过三个表禁止使用JOIN。需要JOIN的字段，数据类型要保持绝对的一致。多表关联查询时，保证被关联的字段需要有索引。



#### 2.5 MySQL内置函数

内置函数按照功能进行划分，分为数值函数，字符串函数，日期和时间函数，流程控制函数，加密与解密函数，获取MySQL信息函数，聚合函数等。

##### 1)单行函数

###### 1.数值函数

![ ](E:\TyporaNotes\MySQL\图片\image-20220829145309656.png)

###### 2.角度与弧度互换函数

![image-20220829203201788](E:\TyporaNotes\MySQL\图片\image-20220829203201788.png)

###### 3.三角函数

![image-20220829203228207](E:\TyporaNotes\MySQL\图片\image-20220829203228207.png)

###### 4,指数与对数

![image-20220829205305565](E:\TyporaNotes\MySQL\图片\image-20220829205305565.png)

###### 5.进制之间的转换函数

![image-20220829205810332](E:\TyporaNotes\MySQL\图片\image-20220829205810332.png)

###### 6.字符串函数

![image-20220829221516599](E:\TyporaNotes\MySQL\图片\image-20220829221516599.png)

> 字符串的索引是从1开始的

![image-20220829225640401](E:\TyporaNotes\MySQL\图片\image-20220829225640401.png)

![image-20220829230719361](E:\TyporaNotes\MySQL\图片\image-20220829230719361.png)

###### 7.获取日期，时间函数

![image-20220830103619246](E:\TyporaNotes\MySQL\图片\image-20220830103619246.png)

###### 8.日期与时间戳的转换函数

![image-20220830103638761](E:\TyporaNotes\MySQL\图片\image-20220830103638761.png)

###### 9.获取月份，星期，星期数，天数等函数

 ![image-20220830103806676](E:\TyporaNotes\MySQL\图片\image-20220830103806676.png)

###### 日期的操作函数

![image-20220830103827685](E:\TyporaNotes\MySQL\图片\image-20220830103827685.png)

![image-20220830103845868](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20220830103845868.png)

![image-20220830111234397](E:\TyporaNotes\MySQL\图片\image-20220830111234397.png)

###### 10.时间和秒钟的转换函数

![image-20220830103909776](E:\TyporaNotes\MySQL\图片\image-20220830103909776.png)

###### 11.计算日期和时间的函数

![3](E:\TyporaNotes\MySQL\图片\image-20220830103942630.png)

![image-20220830104003642](E:\TyporaNotes\MySQL\图片\image-20220830104003642.png)

###### 12.日期的格式化与解析函数

![image-20220830104107342](E:\TyporaNotes\MySQL\图片\image-20220830104107342.png)

![image-20220830151812774](E:\TyporaNotes\MySQL\图片\image-20220830151812774.png)

![image-20220830152405796](E:\TyporaNotes\MySQL\图片\image-20220830152405796.png)

![image-20220830153142384](E:\TyporaNotes\MySQL\图片\image-20220830153142384.png)

![image-20220830153457698](E:\TyporaNotes\MySQL\图片\image-20220830153457698.png)

###### 13.流程控制函数

![image-20220830154131640](E:\TyporaNotes\MySQL\图片\image-20220830154131640.png)

###### 14.加密与解密函数

![image-20220830162423748](E:\TyporaNotes\MySQL\图片\image-20220830162423748.png)

> 在MySQL8.0以上版本，对PASSWORD(str)，ENCODE(value,password_seed),ENCODE(value,password_seed)三个函数均已弃用。

###### 15.MySQL信息函数

![image-20220831135839161](E:\TyporaNotes\MySQL\图片\image-20220831135839161.png)

###### 16.其他函数

![image-20220831140325941](E:\TyporaNotes\MySQL\图片\image-20220831140325941.png)

与ROUND(x,y)函数不同，FORMAT(value,n)函数中如果n的值小于或者等于0，则只保留整数部分。

 ##### 2) 聚合函数





























































