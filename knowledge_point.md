



## 知识杂记

### 1. @RequestMapping中produces属性

作用:设置返回值类型及编码方式

使用注意:必须和@ResponseBody一起使用

好处:1.防止中文乱码

​		2.浏览器中显示好看点，json自动格式化，支持搜索



### 2.JSON序列化与实现Serializable接口序列化

想要序列化某些类的对象，这些类就必须实现Serializable接口。
Serializable这个空接口作用：目的只是简单的标识一个类的对象可以被序列化。
那么什么情况下才需要实例化该接口呢？
	a）当你想把的内存中的对象写入到硬盘的时候；
	b）当你想用套接字在网络上传送对象的时候（另一个说法就是当我们需要把对象的状态信息通过网络进行传输，或者需要将对象的状态信息持久化，以便将来使用时都需要把对象进行序列化。）；
	c）当你想通过RMI传输对象的时候； 分别解释下这三个场景下的使用：
		1)比如说你的内存不够用了，那计算机就要将内存里面的一部分对象暂时的保存到硬盘中，等到要用的时候再读入到内存中，硬盘的那部分存储空间就是所谓的虚拟内存。在比如过你要将某个特定的对象保存到文件中，我隔几天在把它拿出来用，那么这时候就要实现Serializable接口；
		2)在进行java的Socket编程的时候，你有时候可能要传输某一类的对象，那么也就要实现Serializable接口；最常见的你传输一个字符串，它是JDK里面的类，也实现了Serializable接口，所以可以在网络上传输。
		3)如果要通过远程的方法调用（RMI）去调用一个远程对象的方法，如在计算机A中调用另一台计算机B的对象的方法，那么你需要通过JNDI服务获取计算机B目标对象的引用，将对象从B传送到A，就需要实现序列化接口。

> 实现Serializable接口序列化使用于微服务，用于数据传输

Json序列化和实现Serializable接口序列化的区别:

- Json序列化只包含数据内容，而实现Serializable接口序列化则另外存储了类的信息
- 使用场景:json使用于前后端分离数据传输，而实现Serialiazable接口序列化使用于微服务之间数据传输



### 3.ArrayList，HashMap指定类型与不指定类型的区别

1. 避免ClassCastException
2. 避免过多的类型强转
3. 泛型只是保证类型安全，在运行期间还是会擦除

### 4. SecureRandom.getInstanceStrong的nextInt方法在Linux机器上可能会存在阻塞

产生原因:

网上具体原因分析帖：https://www.jb51.net/article/232584.htm 

> private Random rand = SecureRandom.getInstanceStrong()；
>
> rand.nextDouble() 和rand.nextInt()等同类方法在Linux机器上获取时会读取操作系统的/dev/random文件来生成随机数。
>
> 如果你们的机器上没有该文件或系统产生的扰动很小时，就会产生线程阻塞。
>
> 这个文件的数据来源于系统的扰动，比如键盘的输入、鼠标点击等等操作。当系统产生扰动很少的时候，就会导致读取这个文件的线程阻塞（和采用的代码语言无关）。操作系统产生的这个扰动可以通过 cat /proc/sys/kernel/random/entropy_avail 查看（即系统熵值）。

解决方案:

- 采用 new SecureRandom() 实例化，这个实例化底层是读取的是操作系统的 /dev/urandom文件，这个是伪随机，相比 SecureRandom.getInstanceStrong() 方式，它的随机性稍微弱一些，但大多数场景够用了。 (实现起来比较好的方式)
- 仍然采用 SecureRandom.getInstanceStrong() 方式，这种方式适合对随机性要求高的场景。采用这种方式为了避免阻塞问题，就需要系统产生足够的扰动。于是可以安装 haveged 并启动 haveged，启动haveged后可以发现 cat /proc/sys/kernel/random/entropy_avail 返回的值变大了，所以不再阻塞。也可以安装rng-tools这个来达到这个目的
- 直接使用静态成员变量private static Random random  = new Random；(比较不好的方式)

### 5. 关于BigDecimal的一些使用建议

**初始化的两种方式:**

- 在使用BigDecimal构造函数时，尽量传递字符串，而不传递浮点类型
- 采用BigDecimal.valueOf方法来构造初始化值

**equals方法和compareTo方法**

- equals方法不仅比较了值是否相等，还比较了精度是否相同
- compareTo方法实现了Comparable接口，真正比较的是值的大小

通常情况下，如果比较的是连个BigDecimal值的大小，采用其实现的compareTo方法，如果严格限制精度的比较，那么则使用equals方法。

>  在使用BigDecimal进行运算时，一定要明确指明精度和舍入模式。

> 根据数据结果展示格式不同，采用不同的字符串输出方式，通常使用比较多的方法是toPlainString()
>
> toString()方法在必要时会输出科学计数法

### 6. SectorEnum[] enumAry = SectorEnum.values();数组的顺序是怎么样的?

按照枚举类中定义的顺序

### 7.过滤器，拦截器，切面

- 过滤器可以拦截一切请求包括html,css,js等，而拦截器只可以拦截controller中的请求
- 过滤器底层是使用回调函数实现的，而拦截器则是使用动态代理和反射实现的
- 过滤器依赖于tomcat,而拦截器是spring中的，application等也可以使用
- 过滤器和拦截器需要从request中获取参数,而切面通过ponit.getArgs直接获取
- 拦截器作用于请求前，过滤器作用于请求前后，而切面可以在对方法前后，异常，环绕等做处理
- ![image-20221205165612500](.\图片\image-20221205165612500.png)

### 8.JDK动态代理和CGLib动态代理

![image-20221205165900934](.\图片\image-20221205165900934.png)



### 9.linux中rz命令和xftp

- rz命令通常用于传输小文件，而xftp用于传输大文件

- rz命令不能上传文件夹，而sftp可以

- rz命令必须支持ZModem协议

### 10.数据库语句

> insert into ... on duplicate key update ... values() 
>
> 作用：primary key 或 unique 索引出现重复,执行更新操作;若不重复，则执行插入操作
>
> **该语句可以指定替换某一列的值**
>
> 该语句需要特别注意的地方：
>
> 发生冲突时，执行更新操作，但仍然会占用自增id值

replace insert into 

### 11.工厂模式

使用简单工厂模式还是抽象工厂模式取决于产品等级结构是否单一；

简单工厂模式适用于单一的产品等级的商品；抽象工厂模式适用于产品族的商品

简单工厂模式虽然可以解决开闭原则问题，但还是会有单一职责的问题；

抽象工厂模式则将上述问题都给解决了。

### 12.断言

断言抛出的是一个错误，不可以用try{}catch{}进行捕捉。

### 13. 索引优化示例

select * from order where order_state = 4 order by add_time asc limit 10;

表中创建了idx_order_state和idx_add_time两个索引

如果使用上面的sql的话,mysql会走idx_add_time索引，对idx_add_time索引树进行扫描，判断回表判断order_state是否是4,按需取出10条数据，这样比较慢

- 方式一:

```
修改后sql:

select * from order where order_state = 4 order by add_time+0 asc limit 10;

这样add_time改成表达式了，mysql就不会走add_time索引，而是走order_state索引，先筛选出order_state为4的，在进行排序
```

- 方式二:

```
也可以改写成

select *  from order  force index(idx_order_state) where order_state = 4 order by add_time asc limit 10;

不过不建议使用这种形式，这样跳过了mysql的优化器，就不会走更优的索引了。
```

- 方式三：

```
建立order_state和add_time的联合索引
```

###  14.查看linux机器日志命令

不要使用cat ,vim命令，cat,vim 命令会将日志全部加载到内容当中，

最好使用tail 和 less

### 15.FileUtils.copyInputStreamToFile(uploadFile.getInputStream(), file);

```
file文件的名称不能太长。
linux系统中，文件名称不能超过255字符
一个中文占用3个字符，故文件名中最多87中文
```



### 16.























































