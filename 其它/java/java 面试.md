# Java 面试准备

## 自我介绍,项目经历

## Java 基础

- Integer的缓存机制
- 方法重写与重载
- transient、instanceof、volatile、synchronized、final、static、const  关键字用法
- 各种集合类区别
- IO模型，同步、异步、阻塞、非阻塞，BIO、NIO和AIO的用法
- 动态代理&序列化
- 定义SPI、SPI的实现原理

## Java 源码，第三方框架源码&Spring

- hashMap 原理, java8的改变
- AQS
- spring core & spring mvc
- spring Bean的生命周期

## jvm & 多线程

### 多线程相关

- java 内存模型，happens-before、内存屏障、编译器指令重排和CPU指令重排
- 锁对象描述, 锁优化、锁消除、锁粗化、自旋锁、可重入锁、阻塞锁、死锁
- 死锁如何排查
- synchronized, volatile 怎么使用
- 线程池实现原理和常用参数,系统自带的线程池
- CAS(compare and sweep) & AQS (abstract queue synchronizer) & ReentrantLock & Atomic & UnSafe
- CPU缓存，L1，L2，L3和伪共享
- 怎样唤醒一个阻塞的线程


#### 多线程辅助类
- Semaphore （信号量）acquire(1), release(1)
- CountDownLatch 在完成一组正在其他线程中执行的操作之前，允许一个或多个线程一直等待（比如有一个任务A，它要等待其他4个任务执行完毕之后才能执行）latch.countDown()，latch.await()
- CyclicBarrier 允许一组线程互相等待，直到到达某个公共屏障点, Barrier.await()


### JVM相关

- jvm 结构,运行时数据
- jvm 参数调优
- jvm 性能监控与处理工具
- GC 算法与内存分配策略
- class 文件格式, 字节码
- 类加载器&类加载机制
- JIT优化


## 数据结构&算法

### 数据结构

- 队列&双端队列&优先级队列&堆
- 栈
- 字典
- 树&二叉树&平衡二叉树&二叉搜索树(BST)&红黑树&B,B+,B*树&LSM树
- BitSet

### 常用算法

#### 搜索

#### 排序

- 快排

## 设计模式

- 单例模式  单例的七种写法 enum综合考虑质量最高(类的初始化仅会被执行一次)
- 工厂模式
- 建造者模式
- 适配器模式
- 桥接模式
- 代理模式
- 模版方法模式
- 观察者模式
- 策略模式
- 访问者模式

## 计算机网络

- tcp 状态及转换
- TCP三次握手与四次关闭，流量控制和拥塞控制、OSI七层模型、tcp粘包与拆包

## 分布式&中间件&微服务

### SpringCloud

- 服务发现与注册 Eureka、Zookeeper、Consul
- 负载均衡：Feign、Spring Cloud Loadbalance
- 配置中心：Spring Cloud Config
- 限流与熔断：Hystrix
- 链路追踪：dapper,zipkin
- 网关