## Volatile

### volatile原子可见性

**Java内存模型规定在多线程情况下，线程操作主内存（类比内存条）变量，需要通过线程独有的工作内存（类比CPU高速缓存）拷贝主内存变量副本来进行。此处的所谓内存模型要区别于通常所说的虚拟机堆模型**

![work-memory](https://mynamelancelot.github.io/img/concurrent/work-memory.png)

![work-memory](https://mynamelancelot.github.io/img/concurrent/work-memory.png)

> 如果是一个大对象，并不会从主内存完全拷贝一份，而是这个被访问对象引用的对象、对象中的字段可能存在拷贝

**线程独有的工作内存和进程内存（主内存）之间通过8中原子操作来实现**

![main-work-swap](https://mynamelancelot.github.io/img/concurrent/main-work-swap.png)`read load`	         从主存复制变量到当前工作内存

`use assign`          执行代码，改变共享变量值，可以多次出现

`store write`        用工作内存数据刷新主存相关内容

这些操作并不是原子性，也就是在`read load`之后，如果主内存变量发生修改之后，线程工作内存中的值由于已经加载，不会产生对应的变化，所以计算出来的结果会和预期不一样，对于volatile修饰的变量，**jvm虚拟机只是保证从主内存加载到线程工作内存的值是最新的**。

### volatile的禁止指令重排序

`volatile`变量的禁止指令重排序是基于内存屏障（Memory Barrier）实现**【synchronized不具有此功能】**。内存屏障又称内存栅栏，是一个CPU指令，**内存屏障会导致JVM无法优化屏障内的指令集**。

- 对`volatile`变量的写指令后会加入写屏障，对共享变量的改动，都同步到主存当中
- 对`volatile`变量的读指令前会加入读屏障，对共享变量的读取，加载的是主存中最新数据