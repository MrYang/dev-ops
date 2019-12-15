## CPU,内存，硬盘, 网络速度比较

### 时间换算

1s = 1000ms(毫秒)
1ms = 1000μs(微秒)
1μs = 1000ns(纳秒)
1ns = 1000ps(皮秒)

### CPU

主频2.6G 每秒可以执行 2.6*10^9, 每个指令需要 0.38ns

- 一级缓存读取时间为 0.5ns
- 分支预测错误需要耗时 5ns
- 二级缓存读取时间为 7ns
- 互斥锁的加锁和解锁时间需要 25ns
- CPU上下文切换(系统调用）需要大约 1500ns (采用的是单核 CPU 线程平均时间)

- 总核数 = 物理CPU个数 X 每颗物理CPU的核数 
- 总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

- 查看物理CPU个数 `cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l`

- 查看每个物理CPU中core的个数(即核数) `cat /proc/cpuinfo| grep "cpu cores"| uniq`

- 查看逻辑CPU的个数 `cat /proc/cpuinfo| grep "processor"| wc -l`

中断(interrupt)、上下文切换(context swap)、可运行队列(run queue)负载、CPU 利用率来监测 CPU 的性能

### 内存

- 内存寻址100ns
- 内存中读取 1MB 的连续数据，大约为 250μs

### 硬盘

- SSD 随机读取耗时为 150μs
- SSD 读取 1MB 的顺序数据，大约需要 1ms

- 磁盘寻址时间为 10ms
- 从磁盘读取 1MB 连续数据需要 20ms

### 网络

- 1Gbps 的网络上传输 2K 的数据需要 20μs
- 同一个数据中心网络上跑一个来回需要 0.5ms
- 世界上不同城市网络上走一个来回，平均需要 150ms

## 监控工具

- top
- uptime
- vmstat
- ps
- iostat
- sar
- netstat
- tcpdump
- tcptrace
- strace
