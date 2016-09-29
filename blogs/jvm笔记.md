# JVM

### 组成结构

- 程序计数器
- 虚拟机栈
- 本地方法栈
- 堆（年轻代(young)+年老代(tenured)），其中年轻代又分为eden+2个survivor区，比例是8:1:1
- 方法区也叫永久代(PermGen,包括常量池 (内存池)，字段与方法数据及代码),JVM1.8已移除，参数PermSize 和 MaxPermSize会被忽略
- Metaspace JVM1.8新加入，替代PermGen

### jvm参数

- -Xms 初始堆 默认物理内存的1/64
- -Xmx 最大堆 默认物理内存的1/4
- -Xmn 年轻代 Sun官方推荐配置为整个堆的3/8
- -Xss 线程大小
- -XX:NewSize JVM堆的‘年轻代’的默认大小
- -XX:MaxNewSize 设置JVM堆的‘年轻代’的最大大小
- -XX:OldSize 设置JVM堆的‘年老代’的大小
- -XX:PermSize 初始永久代(1.8移除)
- -XX:MaxPermSize 永久代最大值(1.8移除)
- -XX:NewRatio ‘年老代’和‘年轻代’的大小比率 默认是8,即 年老代:年轻代比例为8:2
- -XX:SurvivorRatio 年轻代eden与两个survivor之间的比例，默认是8,即8:1:1
- -XX:+PrintGCDetails 打印垃圾回收详细信息
- -XX:+PrintGCTimeStamps 打印GC详细时间
- -Xloggc:../logs/gc.log gc日志
- -XX:+HeapDumpOnOutOfMemoryError JVM在发生内存溢出时自动生成堆内存快照,快照保存在JVM的启动目录下名为java_pid<pid>.hprof,文件会很大
- -XX:HeapDumpPath=<path> 堆内存快照生成路径
- -XX:+PrintTenuringDistribution 指定JVM 在每次新生代GC时，输出幸存区中对象的年龄分布

### gc 日志分析

```xml
2015-05-26T14:45:37.987-0200:151.126:
[GC(Allocation Failure)
151.126: [DefNew:629119K->69888K(629120K), 0.0584157 secs]
1619346K->1273247K(2027264K),0.0585007 secs]
[Times: user=0.06 sys=0.00, real=0.06 secs]
```

其中第一行表示GC事件(GC event)开始的时间点，及JVM的启动时间，单位为秒。

第二行GC表示GC类型，（`GC`表示minor GC，`Full GC`表示Full GC） 及引起垃圾回收的原因

第三行垃圾回收器名称，629119K->69888K，本次垃圾收集之前和之后的年轻代内存使用情况，（629120K）年轻代的总的大小，花费0.0584157秒

第四行，1619346K->1273247K在本次垃圾收集之前和之后整个堆内存的使用情况，(2027264K) – 总的可用的堆内存

第五行，user＝0.06 – 此次垃圾回收, 垃圾收集线程消耗的所有CPU时间,sys=0.00 操作系统调用(OS call) 以及等待系统事件的时间,real=0.06 应用程序暂停的时间(Clock time). 由于串行垃圾收集器(Serial Garbage Collector)只会使用单个线程, 所以 real time 等于 user 以及 system time 的总和

### jvm垃圾回收器

Serial，ParNew（Serial的多线程版本），Parallel Scavenge（使用复制算法，并行，从吞吐量出发），这三个收集器用于新生代回收

Serial Old，Parallel Old， CMS(Concurrent Mark Sweep)是以获取最短回收暂停时间为目标的收集器，用于老年代收集

- -XX:+UseSerialGC  Seiral串行垃圾回收器（Serial收集器），单线程环境而设计,-client模式下，默认该回收器，stop world
- -XX:+UseParallelGC    Parallel并行吞吐优先垃圾回收器（ParNew收集器），64位操作系统使用-server模式，默认垃圾回收器，stop world
- -XX:ParallelGCThreads=<value> Parallel并行吞吐优先垃圾回收器线程个数，默认是cpu个数
- -XX:+UseConcMarkSweepGC 	CMS并发标记扫描垃圾回收器，CMS垃圾回收器是对并行垃圾回收器的一个优化，它以CPU和系统资源为代价，换取GC的延迟，使用该选项后，新生代默认使用ParNew收集器。
- -XX:ParallelCMSThreads=<value>  CMS并发标记扫描垃圾回收器线程数，通常是cpu个数
- -XX:+UseG1GC	G1垃圾回收器

### GC

minor GC, 年轻代gc，比较频繁，时间短。Minor GC总是在不能为新的对象分配空间的时候触发, 例如 Eden区满了，分配空间的越快，Minor GC越频繁.每次Minor GC只会清理年轻代

Major GC, 当堆空间已满或者年老代区占满时，就会触发 Major GC,清理年老区（Tenured space）

full GC, 全gc，清理整个内存堆 – 既包括年轻代也包括年老代. 比较少，时间长

### JVM性能调优监控工具

#### jps 显示指定系统内所有的 HotSpot 虚拟机进程

-q:只输出LVMID，省略主类的名称

-m:输出虚拟机进程启动时传给主类main()函数的参数

-l:输出主类的全类名，如果进程执行的是Jar包，输出Jar路径

-v:输出虚拟机进程启动时JVM参数

命令格式：`jps [option] [pid]`

#### jinfo 显示虚拟机配置信息

jinfo可以使用如下选项：

-flag:显示未被显示指定的参数的系统默认值

-flag [+|-]name或-flag name=value: 修改部分参数

-sysprops:打印虚拟机进程的System.getProperties()

命令格式: `jinfo [option] pid`

#### jmap  生产虚拟机的内存快照 dump 文件

-dump:生成java堆转储快照

-heap:显示java堆详细信息(只在Linux/Solaris下有效)

-F:当虚拟机进程对-dump选项没有响应时，可使用这个选项强制生成dump快照(只在Linux/Solaris下有效)

-finalizerinfo:显示在F-Queue中等待Finalizer线程执行finalize方法的对象(只在Linux/Solaris下有效)

-histo[:live]:显示堆中对象统计信息

-permstat:以ClassLoader为统计口径显示永久代内存状态(只在Linux/Solaris下有效)

命令格式: `jmap [option] pid`，

`jmap -dump:format=b,file=dumpFileName pid` dump生成的文件可以使用jhat，visualVm查看

#### jhat 分析 dump 文件

使用方法为： `jhat -port 9998 -J-Xmx512m [file]`

#### jstack  显示虚拟机的线程快照

用于生成当前JVM的所有线程快照，线程快照是虚拟机每一条线程正在执行的方法,目的是定位线程出现长时间停顿的原因。

-F:当正常输出的请求不被响应时，强制输出线程堆栈

-l:除堆栈外，显示关于锁的附加信息

-m:如果调用到本地方法的话，可以显示C/C++的堆栈

命令格式: `jstack [option] pid`

#### jstat  用于收集 HotSpot 虚拟机各方面的运行数据

jstat 选项

- -class:监视类装载、卸载数量、总空间及类装载所耗费的时间
- -gc:监听Java堆状况，包括Eden区、两个Survivor区、老年代、永久代等的容量，以用空间、GC时间合计等信息
- -gccapacity:监视内容与-gc基本相同，但输出主要关注java堆各个区域使用到的最大和最小空间
- -gcutil:监视内容与-gc基本相同，但输出主要关注已使用空间占总空间的百分比
- -gccause:与-gcutil功能一样，但是会额外输出导致上一次GC产生的原因
- -gcnew:监视新生代GC状况
- -gcnewcapacity:监视内同与-gcnew基本相同，输出主要关注使用到的最大和最小空间
- -gcold:监视老年代GC情况
- -gcoldcapacity:监视内同与-gcold基本相同，输出主要关注使用到的最大和最小空间
- -gcpermcapacity:输出永久代使用到最大和最小空间
- -compiler:输出JIT编译器编译过的方法、耗时等信息
- -printcompilation:输出已经被JIT编译的方法

命令格式 `jstat [option pid [interval[s|ms] [count]]]`  后面两个参数表示间隔时间及总输出行数

输出各列含义为：

S0C：S0区容量（S1区相同，略）(Survivor 0区 Capacity)

S0U：S0区已使用 (Survivor 0区 Used)

EC：E区容量 (Eden Capacity)

EU：E区已使用 (Eden Used)

OC：老年代容量 (Old Capacity)

OU：老年代已使用 (Old Used)

PC：Perm容量 (Perm Capacity) 1.8移除

PU：Perm区已使用 (Perm Used) 1.8 移除

MC：Metaspace容量 1.8新增

MU: Metaspace区已使用 1.8新增

YGC：Young GC（Minor GC）次数

YGCT：Young GC总耗时

FGC：Full GC次数

FGCT：Full GC总耗时

GCT：GC总耗时


#### visualvm

- Visual GC插件

Eden Space(200M, 100M):20M, 表示Eden 总共分配200M空间，当前分配100M，其中20M已被使用

#### jconsole
