## Volatile全解读

未使用Volatile的双重检查锁：会有问题

解决方案：

1. 使用volatile修改变量

> 大众解读：volatile禁止了new对象里面三行代码的重排序。(目前我是这样理解的)
>
> 大神解读：new Instance是一个JVM指令吗，对应的是new指令。Voatile能够保障单个JVM指令的原子性，所以new Instance相当于是volatile写，会在new Instance前加storestore,后加storeload屏障，B线程就必须在storeload屏障后边读取。实质撒花姑娘，new对象里面的三个步骤依然可以重排序，真正的控制是在外层的内存屏障控制。

1. 基于类初始化的解决方案

   > JVM在类的初始化阶段（即在Class被加载后，且被线程使用之前），会执行类的初始化。在执行类的初始化期间，JVM会去获取一个锁。这个锁可以同步多个线程对同一个类的初始化。