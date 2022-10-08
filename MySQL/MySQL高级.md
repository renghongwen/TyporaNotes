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

解析ibd文件命令

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















 















