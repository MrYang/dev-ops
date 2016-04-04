# Zookeeper

### 安装配置

- 单机模式

从官网下载稳定zookeeper版本，解压`tar -zxvf zookeeper-3.3.4.tar.gz -C /usr/local/` 至` /usr/local/zookeeper-3.4`

复制`conf/zoo_sample.cfg`为`zoo.cfg`，新建data,logs目录，并修改`zoo.cfg`配置为:
```shell
tickTime=2000  #Zookeeper 服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个 tickTime 时间就会发送一个心跳
dataDir=/usr/local/zookeeper-3.4/data  #数据目录
dataLogDir=/usr/local/zookeeper-3.4/logs  #日志目录
clientPort=2181  #客户端连接端口
```

zookeeper命令 `bin/zkServer.sh {start|start-foreground|stop|restart|status|upgrade|print-cmd}`

添加开机启动 `echo /usr/local/zookeeper-3.4/bin/zkServer.sh start >> /etc/rc.local`

- 伪集群模式

复制zookeeper目录2份，分别为zookeeper0, zookeeper1, zookeeper2, 修改zookeeper0实例`zoo.cfg`
```shell
tickTime=2000
dataDir=/usr/local/zookeeper0/data
dataLogDir=/usr/local/zookeeper0/logs
clientPort=2181

initLimit=5  #Zookeeper的Leader 接受客户端（Follower）初始化连接时最长能忍受多少个心跳时间间隔数。当已经超过 5个心跳的时间（也就是tickTime）长度后 Zookeeper 服务器还没有收到客户端的返回信息，那么表明这个客户端连接失败。总的时间长度就是 5*2000=10 秒
syncLimit=2 #表示 Leader 与 Follower 之间发送消息时请求和应答时间长度，最长不能超过多少个tickTime 的时间长度，总的时间长度就是 2*2000=4 秒。
server.0=127.0.0.1:8880:7770  #server.A=B：C：D：其中A 是一个数字，表示这个是第几号服务器；B 是这个服务器的 ip 地址；C 表示的是这个服务器与集群中的 Leader 服务器交换信息的端口；D 表示的是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举，选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口。如果是伪集群的配置方式，由于 B 都是一样，所以不同的 Zookeeper 实例通信端口号不能一样，所以要给它们分配不同的端口号。
server.1=127.0.0.1:8881:7771
server.2=127.0.0.1:8882:7772
```
同样修改zookeeper1,zookeeper2配置，修改dataDir，dataLogDir，clientPort几个配置项

在各个zookeeper dataDir目录中新建myid文件，分别写入0,1,2，分别对应配置文件中 server配置项

任意进入一个zookeeper实例，启动。

- 集群模式

集群模式跟伪集群模式几乎一样，因为各个zookeeper实例是分布在不同机器上，所以配置文件可以是一样，不用修改端口，dataDir， DataLogDir, clientPort等配置项
