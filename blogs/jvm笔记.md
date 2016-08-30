# JVM

### 组成结构

- 程序计数器
- 虚拟机栈
- 本地方法栈
- 堆（年轻代(young)+年老代(tenured)），其中年轻代又分为eden+2个survivor区，比例是8:1:1
- 方法区也叫永久代(PermGen,包括常量池 (内存池)，字段与方法数据及代码)

![](http://images.cnblogs.com/cnblogs_com/jackyrong/a1.jpg)

### jvm参数

- -Xms 初始堆 默认物理内存的1/64
- -Xmx 最大堆 默认物理内存的1/4
- -Xmn 年轻代 Sun官方推荐配置为整个堆的3/8
- -Xss 线程大小

- -XX:NewSize JVM堆的‘年轻代’的默认大小
- -XX:MaxNewSize 设置JVM堆的‘年轻代’的最大大小
- -XX:OldSize 设置JVM堆的‘老生代’的大小
- -XX:PermSize 初始永久代
- -XX:MaxPermSize 永久代最大值
- -XX:NewRatio ‘年轻代’和‘年老代’的大小比率 默认是2,即 年轻代:年老代比例为2:8
- -XX:SurvivorRatio 年轻代eden与两个survivor之间的比例，默认是8,即8:1:1
- -XX:+PrintGCDetails 打印垃圾回收详细信息
- -XX:+PrintGCTimeStamps 打印GC详细时间
- -Xloggc:../logs/gc.log gc日志

### jvm垃圾回收器

- -XX:+UseSerialGC  Seiral串行垃圾回收器，单线程环境而设计,-client模式下，默认该回收器
- -XX:+UseParallelGC    Parallel并行吞吐优先垃圾回收器，64位操作系统使用-server模式，默认垃圾回收器，stop world
- -XX:+UseConcMarkSweepGC 	CMS并发标记扫描垃圾回收器，CMS垃圾回收器是对并行垃圾回收器的一个优化，它以CPU和系统资源为代价，换取GC的延迟
- -XX:ParallelCMSThreads=  CMS并发标记扫描垃圾回收器线程数，通常是cpu个数
- -XX:+UseG1GC	G1垃圾回收器

### GC

minor GC, 年轻代gc，比较频繁，时间短。Minor GC总是在不能为新的对象分配空间的时候触发, 例如 Eden区满了，分配空间的越快，Minor GC越频繁.每次Minor GC只会清理年轻代

Major GC, 当堆空间已满或者年老代区占满时，就会触发 Major GC,清理年老区（Tenured space）

full GC, 全gc，清理整个内存堆 – 既包括年轻代也包括年老代. 比较少，时间长
