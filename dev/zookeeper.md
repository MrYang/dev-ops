# Zookeeper

ZooKeeper是一个高可用的分布式数据管理与系统协调框架。基于对Paxos算法的实现，使该框架保证了分布式环境中数据的强一致性，基于这样的特性，使得ZooKeeper解决很多分布式问题.

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

### 数据模型

Zookeeper 维护一个具有层次关系的数据结构，非常类似于一个标准的文件系统，其中每个节点称之为znode，每个znode由3部分组成：stat： 此为状态信息, 描述该znode的版本, 权限等信息；data： 与该znode关联的数据；children： 该znode下的子节点；

stat 数据结构：

```xml
czxid. 节点创建时的zxid,ZooKeeper状态的每一次改变, 都对应着一个递增的Transaction id, 该id称为zxid. 由于zxid的递增性质, 如果zxid1小于zxid2, 那么zxid1肯定先于zxid2发生. 创建任意节点, 或者更新任意节点的数据, 或者删除任意节点, 都会导致Zookeeper状态发生改变, 从而导致zxid的值增加；
mzxid. 节点最新一次更新发生时的zxid；
ctime. 节点创建时的时间戳；
mtime. 节点最新一次更新发生时的时间戳；
dataVersion. 节点数据的更新次数；
cversion. 其子节点的更新次数；
aclVersion. 节点ACL(授权信息)的更新次数；
ephemeralOwner. 如果该节点为ephemeral节点, ephemeralOwner值表示与该节点绑定的session id. 如果该节点不是ephemeral节点, ephemeralOwner值为0. 节点类型：persistent. persistent节点不和特定的session绑定, 不会随着创建该节点的session的结束而消失, 而是一直存在, 除非该节点被显式删除.ephemeral. ephemeral节点是临时性的, 如果创建该节点的session结束了, 该节点就会被自动删除. ephemeral节点不能拥有子节点. 虽然ephemeral节点与创建它的session绑定, 但只要该该节点没有被删除, 其他session就可以读写该节点中关联的数据
dataLength. 节点数据的字节数；
numChildren. 子节点个数；
```

znode 特点：

- znode 可以有子节点目录，并且每个 znode 可以存储数据，但是EPHEMERAL 类型的目录节点不能有子节点目录；

- znode 可以是临时节点（EPHEMERAL），一旦创建这个 znode 的客户端与服务器失去联系，这个 znode 也将自动删除，Zookeeper 的客户端和服务器通信采用长连接方式，每个客户端和服务器通过心跳来保持连接，这个连接状态称为 session，如果 znode 是临时节点，这个 session 失效，znode 也就删除了；

- znode 是有版本的，每个 znode 中存储的数据可以有多个版本，也就是一个访问路径中可以存储多份数据；

- znode 的目录名可以自动编号，如 App1 已经存在，再创建的话，将会自动命名为 App2；

- znode 可以被监控，包括这个目录节点中存储的数据的修改，子节点目录的变化等，一旦变化可以通知设置监控的客户端

### curator使用

- 基本操作

```java
public CuratorFramework client() {
    RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
    CuratorFramework client = CuratorFrameworkFactory.newClient("127.0.0.1:2181", retryPolicy);
    client.start();
    return client;
}

public Stat get(String path) throws Exception {
    NodeCache nodeCache = new NodeCache(client, path);
    nodeCache.start(true);
    return nodeCache.getCurrentData().getStat();
}

public String create(String path, String data) throws Exception {
    return client.create().forPath(path, data.getBytes());
}

public String createEphemeral(String path, String data) throws Exception {
    return client.create().withMode(CreateMode.EPHEMERAL).forPath(path, data.getBytes());
}

public String createEphemeralSequential(String path, String data) throws Exception {
    return client.create().withProtection().withMode(CreateMode.EPHEMERAL_SEQUENTIAL).forPath(path, data.getBytes());
}

public Stat set(String path, String newData) throws Exception {
    return client.setData().forPath(path, newData.getBytes());
}

public Void delete(String path) throws Exception {
    return client.delete().forPath(path);
}

/**
* 分布式锁
*/
public void lock(String lockPath) throws Exception {
    InterProcessMutex lock = new InterProcessMutex(client, lockPath);
    if (lock.acquire(10, TimeUnit.SECONDS)) {
        try {
            logger.info("i get a lock");
        } finally {
            lock.release();
        }
    }
}

public void leader(String path) {
    LeaderSelectorListener listener = new LeaderSelectorListenerAdapter() {
        public void takeLeadership(CuratorFramework client) throws Exception {
            logger.info("now i'm leader");
        }
    };

    LeaderSelector selector = new LeaderSelector(client, path, listener);
    selector.autoRequeue();  // not required, but this is behavior that you will probably expect
    selector.start();
}

/*
    Path Cache：监视一个路径下1）孩子结点的创建、2）删除，3）以及结点数据的更新。产生的事件会传递给注册的PathChildrenCacheListener。涉及到：PathChildrenCache，PathChildrenCacheEvent，PathChildrenCacheListener，ChildData
    Node Cache：监视一个结点的创建、更新、删除，并将结点的数据缓存在本地。涉及到：NodeCache，NodeCacheListener，ChildData
    Tree Cache：Path Cache和Node Cache的“合体”，监视路径下的创建、更新、删除事件，并缓存路径下所有孩子结点的数据，涉及到：TreeCache，TreeCacheListener，TreeCacheEvent，ChildData
*/
public void addWatcher() {
  PathChildrenCache watcher = new PathChildrenCache(client, "/zookeeper", true);
  watcher.getListenable().addListener((client1, event) -> {
      ChildData data = event.getData();
      if (data == null) {
          System.out.println("No data in event[" + event + "]");
      } else {
          System.out.println("Receive event: "
                  + "type=[" + event.getType() + "]"
                  + ", path=[" + data.getPath() + "]"
                  + ", data=[" + new String(data.getData()) + "]"
                  + ", stat=[" + data.getStat() + "]");
      }
  });
  watcher.start(PathChildrenCache.StartMode.BUILD_INITIAL_CACHE);
}

```
