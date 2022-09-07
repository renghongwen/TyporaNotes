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

![image-20220823223824387](.\图片\image-20220823223824387.png)

> %：代表零个或多个不确定的字符
>
> _ ： 代表一个不确定的字符

4. OR可以和AND一起使用，并且AND的优先级高于OR。
5. <>符号表示不等于



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

![image-20220828094101829](.\图片\image-20220828094101829.png)

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

![ ](.\图片\image-20220829145309656.png)

###### 2.角度与弧度互换函数

![image-20220829203201788](.\图片\image-20220829203201788.png)

###### 3.三角函数

![image-20220829203228207](.\图片\image-20220829203228207.png)

###### 4,指数与对数

![image-20220829205305565](.\图片\image-20220829205305565.png)

###### 5.进制之间的转换函数

![image-20220829205810332](.\图片\image-20220829205810332.png)

###### 6.字符串函数

![image-20220829221516599](.\图片\image-20220829221516599.png)

> 字符串的索引是从1开始的

![image-20220829225640401](.\图片\image-20220829225640401.png)

![image-20220829230719361](.\图片\image-20220829230719361.png)

###### 7.获取日期，时间函数

![image-20220830103619246](.\图片\image-20220830103619246.png)

###### 8.日期与时间戳的转换函数

![image-20220830103638761](.\图片\image-20220830103638761.png)

###### 9.获取月份，星期，星期数，天数等函数

 ![image-20220830103806676](.\图片\image-20220830103806676.png)

###### 日期的操作函数

![image-20220830103827685](.\图片\image-20220830103827685.png)

![image-20220830103845868](.\图片\image-20220830103845868.png)

![image-20220830111234397](.\图片\image-20220830111234397.png)

###### 10.时间和秒钟的转换函数

![image-20220830103909776](.\图片\image-20220830103909776.png)

###### 11.计算日期和时间的函数

![3](.\图片\image-20220830103942630.png)

![image-20220830104003642](.\图片\image-20220830104003642.png)

###### 12.日期的格式化与解析函数

![image-20220830104107342](.\图片\image-20220830104107342.png)

![image-20220830151812774](.\图片\image-20220830151812774.png)

![image-20220830152405796](.\图片\image-20220830152405796.png)

![image-20220830153142384](.\图片\image-20220830153142384.png)

![image-20220830153457698](E:\TyporaNotes\MySQL\图片\image-20220830153457698.png)

###### 13.流程控制函数

![image-20220830154131640](.\图片\image-20220830154131640.png)

###### 14.加密与解密函数

![image-20220830162423748](.\图片\image-20220830162423748.png)

> 在MySQL8.0以上版本，对PASSWORD(str)，ENCODE(value,password_seed),ENCODE(value,password_seed)三个函数均已弃用。

###### 15.MySQL信息函数

![image-20220831135839161](.\图片\image-20220831135839161.png)

###### 16.其他函数

![image-20220831140325941](.\图片\image-20220831140325941.png)

与ROUND(x,y)函数不同，FORMAT(value,n)函数中如果n的值小于或者等于0，则只保留整数部分。

 ##### 2) 聚合函数

###### 1.常见的聚合函数:

- AVG：只使用于数值类型的字段
- SUM：只使用于数值类型的字段
- MAX
- MIN
- COUNT：计算指定字段在查询结果中出现的个数（**计算指定字段出现的个数时，是不计算NULL值的**）

**以上函数在计算时都不计算NULL值 **                              AVG = SUM / COUNT

MySQL中聚合函数是不能嵌套使用的

> 统计表中的记录数，使用count(*),count(1),count(具体字段)哪个效率比较高?	

​	如果使用的是InnoDB存储引擎，则三者效率为count(*)  = count(1) > count(字段)



###### 2.group by的使用

- 在使用过程中，需要注意，SELECT中出现的非组函数的字段必须声明在group by中。反之，group by中声明的子弹可以不出现在SELECT中。
- SELECT ... FROM ... WHERE ... GROUP BY ... ORDER BY ... LIMIT ...
- MySQL中GROUP BY 中使用WITH ROLLUP  即 GROUP BY ... WITH  ROLLUP
- 当使用ROLLUP时，不能同时使用ORDER BY子句进行结果排序，即ROLLUP和ORDER BY是相互排斥的。

###### 3.having的使用

> 若果过滤条件中使用了聚合函数，则必须使用HAVING来替换WHERE。
>
>  即开发中，使用HAVING的前提是SQL中使用了GROUP  BY。

​	当过滤条件中有聚合函数时，则此过滤条件必须声明在HAVING中。

当过滤条件中没有聚合函数时，则此过滤条件声明在WHERE中或HAVING中都可以。但是，建议声明在WHERE中。



**WHERE和HAVING的比较:**

1. 从适用范围上来说，HAVING使用范围更广。

   - WHERE可以直接使用表中的字段作为筛选条件，但不能使用分组中的计算函数作为筛选条件；HAVING必须要与GROUP BY配合使用，可以把分组计算的函数和分组字段作为筛选条件。

2. 如果过滤条件中没有聚合函数，这种情况下，WHERE的执行效率要高于HAVING

   - 如果需要通过连接从关联表中获取需要的数据，WHERE是先筛选后连接，而HAVING是先连接后筛选。

   

###### 4. SQL底层执行原理

SELECT语句的执行顺序：

> FROM <left_table>
>
> ON <join_condition>
>
> <join_type> JOIN <right_table>
>
> WHERE <where_condition>
>
> GROUP BY <group_by_list>
>
> HAVING <having_condition>
>
> SELECT
>
> DISTINCT <select_list>
>
> ORDER BY <order_by_condition>
>
> LIMIT <limit_number>



#### 2.6 子查询

编写注意事项:

- 子查询要包含在括号内
- 将子查询放在比较条件的右侧
- 单行操作符对应单行子查询，多行操作符对应多行子查询

**子查询的分类：**

1. 角度一：从内查询返回的结果的条目数方面看
   - 单行子查询   |  多行子查询
2. 角度二：内查询是否被执行多次
   -  相关子查询  |  不相关子查询

关联子查询：如果子查询的执行依赖于外部查询，通常情况下都是因为子查询中的表用到了外部的表，并进行了条件关联，因此每执行一次外部查询，子查询都要重新计算一次。 



相关子查询的执行流程：

![image-20220903092345312](.\图片\image-20220903092345312.png)

##### 多行比较操作符

| 操作符 | 含义                                                     |
| ------ | -------------------------------------------------------- |
| IN     | 等于列表中的**任意一个**                                 |
| ANY    | 需要和单行比较符一起使用，和子查询返回的**某一个值**比较 |
| ALL    | 需要和单行比较符一起使用，和子查询返回的**所有值**比较   |
| SOME   | 实际上是ANY的别名，作用相同，一般常使用ANY               |

> 在SELECT中，除了group by 和 limit 之外，其他位置都可以声明子查询



##### EXISTS 和 NOT EXISTS关键字

使用场景：关联子查询通常也会和EXISTS操作符一起来使用，用来检查在子查询中是否存在满足条件的行。

```sql
-- 查询departments表中，不存在于employees表中的部门的department_id和department_name
select d.department_id,d.department_name
from departments d 
where not exists (select * 
				 from employees e
				 where d.department_id = e.department_id);

```

通常情况下，能够使用IN的多行子查询都可以使用EXISTS关键字来完成



一个场景下既可以使用自连接完成，也可以使用子查询完成，那该使用哪种方式?

> 一般建议使用自连接，因为在许多DBMS的处理过程中，对于自连接的处理速度比子查询快得多。
>
> 子查询实际上是通过未知表进行查询后的条件判断，而自连接是通过已知的自身数据表进行条件判断，因此在大部分DBMS中都对自连接处理进行了优化。



练习题：

```sql
-- 查询平均工资最低的部门信息
select * 
from departments d 
where department_id  = (
	select department_id 
	from employees e2 
	group by department_id 
	having  avg(salary) = (
						select min(avg_salary)  
						from (
								select department_id,avg(salary) "avg_salary"
								from employees e 
								group by department_id
						) t_department_avg_salary
	)
);

-- 查询平均工资最低的部门信息和该部门的平均工资
select d.*,department_avg_salary.avg_salary
from departments d, (
		select department_id,avg(salary) "avg_salary"
		from employees e  
		group by department_id
		order by avg_salary asc 
		limit 0,1
) department_avg_salary
where d.department_id  = department_avg_salary.department_id;

-- 各个部门中，最高工资中最低的那个部门的 最低工资是多少?
select min(salary) 
from employees emp,(
			select e.department_id,max(salary) 
			from employees e  
			group by e.department_id    
			order by max(salary) asc
			limit 0,1
) department_max_salary_min
where emp.department_id = department_max_salary_min.department_id;

```



### 三. DDL

#### 3.1管理 数据库

##### 3.1.1 创建数据库

创建数据库并指定字符集

``` sql
 create database 数据库名 character set 字符集;
```

判断数据库是否已经存在，不存在则创建数据库

```sql
create database if not exists 数据库名;
```

##### 3.1.2 管理数据库

查看当前使用的数据库

```sql 
select database() from dual;
```

查看指定数据库下保存的数据表

```sql
show tables from 数据库名;
```

##### 3.1.3 修改数据库(一般不会使用)

修改数据库字符集

```sql
alter database 数据库名 character set 字符集;
```

##### 3.1.4 删除数据库

```sql
drop database 数据库名;
drop dabase if exists 数据库名;
```

慎用删除操作！！！

删除操作默认是不能回滚的

#### 3.2 MySQL中的数据类型

| 类型             | 类型举例                                                     |
| ---------------- | ------------------------------------------------------------ |
| 整数类型         | TINYINT，SMALLINT，MEDIUMINT，INT（或INTEGER），BIGINT       |
| 浮点类型         | FLOAT,DOUBLE                                                 |
| 定点数类型       | DECIMAL                                                      |
| 位类型           | BIT                                                          |
| 日期时间类型     | YEAR，TIME，DATE，DATETIME，TIMESTAMP                        |
| 文本字符串类型   | CHAR，VARCHAR，TINYTEXT，TEXT,MEDIUMTEXT，LONGTEXT           |
| 枚举类型         | ENUM                                                         |
| 集合类型         | SET                                                          |
| 二进制字符串类型 | BINARY，VARBINARY，TINYBLOB，BLOB，MEDIUMBLOB，LONGBLOB      |
| JSON类型         | JSON对象，JSON数组                                           |
| 空间数据类型     | 单值：GEOMETRY，POINT，LINESTRING，POLYGON                   |
|                  | 集合：MULTIPOINT，MULTINESTRING，MULTIPOLYGON，GEOMETRYCOLLECTION |

![](.\图片\微信图片_20220906092451.png)

#### 3.3 表的相关操作

基于现有的表创建表(同时还可以导入数据)

```sql
create table 表名 as select 字段1,字段2 .... from 现有表的表名
```

补充：查询语句中字段的别名，可以作为新创建的表的字段的名称

添加一个字段

```sql
alter table 表名  add  字段名  字段类型 [ first | after 字段名 ] ;
```

修改一个字段（数据类型，长度，默认值）

```sql
alter table 表名 modify  字段名 字段类型(长度)  [default  默认值];
```

重命名一个字段(可以同时对字段长度进行修改)

```sql
alter table 表名 change 原字段名 新字段名 字段类型(长度);
```

删除一个字段

```sql
alter table 表名 drop column 字段名;
```

重命名表

```sql
方式一:

	rename table 原表名 to 新表名;

方式二:

	alter table 原表名  rename to 新表名;
```

删除表(将表结构和表中数据删除，释放表空间)

```sql
drop table [ if exists ] 表名;	
```

清空表(清空表中的所有数据，但表结构保留)

```sql
truncate table 表名;
```

 #### 3.4 DCL中的commit和rollback

> 一旦执行commit操作，则数据就被永久保存到数据库当中，数据无法执行回滚操作。
> 一旦执行rollback操作，可以实现数据的回滚，回滚到最近一次commiit位置

 #### 3.5 truncate table 和 delete from 的区别

相同点：都可以对表中的所有数据进行删除，同时保留表结构

不同点：truncate table 删除表数据后，不可以进行回滚

​				delete from 删除表数据后，可以进行回滚

知识拓展:

> DDL操作一旦操作，便不可以进行回滚(因为一定会执行一次commit操作，而此commit操作不受set autocommit = false的影响)
>
> DML操作一旦操作，可以进行回滚操作，默认情况下是不能进行回滚操作，只有在执行DML操作之前执行了 set autocommit = false 才能进行回滚

#### 3.6 《阿里巴巴Java开发手册》sql部分

- 【参考】truncate table 比delete 速度快，且使用的系统和事务日志资源少，但truncate 无事务且不触发 trigger，有可能造成事故，故不建议在开发代码中使用此语句。

- 【参考】合适的字段存储长度，不但节约数据表空间，节约索引存储，更重要的时提升检索速度。

  - 无符号值可以避免误存负数，且扩大了表示范围。

  

####  3.7 MySQL8.0中DDL的原子化

> 在MySQL8.0版本中，InnoDB表的DDL支持事务完整性，即DDL操作要么成功要么回滚。DDL操作回滚日志写入data dictionary数据字典表mysql.innodb_ddl_log(该表示隐藏的表，通过show tables无法看到)中，用于回滚操作，通过设置参数，可将DDL操作日志打印到MySQL错误日志中。

### 四.DML

#### 4.1 新增数据

方式一：逐条添加数据

```sql
insert into 表名(字段名1,字段名2, ... ) values(字段值1,字段值2, ... ) 
```

一个同时插入多行记录的insert语句等同于多个单行插入的insert语句，但是多行的insert语句在处理过程中效率更高。

方式二：将查询结果插入到表中

```sql
insert into 表名(字段名1,字段名2, ... )
select 字段名1,字段名2, ... 
from 表名
where 条件
```

注意：查询的字段一定要与添加到的表的字段一一对应

#### 4.2 更新数据

```sql
update 表名 set ... where ...
```

#### 4.3 删除数据

```sql
delete from 表名 where ...
```

#### 4.4 MySQL8.0新特性：计算列

> 计算列：某一列的值是通过别栋列计算得来的。

 例：

```sql
create table 表名(
字段1 类型,
字段2 类型,
字段3 generated always as (字段1 + 字段2) virtual  
);
```







 







































































