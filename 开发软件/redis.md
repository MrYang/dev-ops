# Redis

### 安装

- yum 方式安装
	- 安装 EPEL 仓库 `yum install epel-release`
	- 安装redis `yum install redis`
	- 开机启动
		- CentOS 6 `chkconfig redis on`
		- CentOS 7 `systemctl enable redis`
	- redis命令
		- CentOS 6 `service redis start|stop|restart|status`
		- CentOS 7 `systemctl start|stop|restart|status redis`
	
	配置文件位于`/etc/redis.conf`
	
- 源码安装
	- 下载tar安装包`wget http://download.redis.io/releases/redis-2.8.13.tar.gz`
	- 解压`tar -zxvf redis-2.8.13.tar.gz -C /usr/local/`
	- 编译安装`make && make install`
	- 修改redis.conf配置文件 `daemonize yes`
	- 启动 `/usr/local/redis/bin/redis-server /usr/local/redis/conf/redis.conf`
	- 关闭 `pkill redis-server` 或者 `./redis-cli shutdown`
	- 设置开机启动`echo "/usr/local/redis/bin/redis-server /usr/local/redis/conf/redis.conf &" >> /etc/rc.local`

### 配置

```shell
daemonize yes  #以后台daemon方式运行redis
pidfile "/var/run/redis.pid"  #redis以后台运行，默认pid文件路径/var/run/redis.pid
port 6379  #默认端口
bind 127.0.0.1 #默认绑定本机所有ip地址，为了安全，可以只监听内网ip
timeout 300 #客户端超时设置，单位为秒
loglevel verbose  #设置日志级别，支持四个级别：debug、notice、verbose、warning
logfile stdout  #日志记录方式，默认为标准输出，logs不写文件，输出到空设备/deb/null
logfile "/usr/local/redis/var/redis.log"  #可以指定日志文件路径
databases 16  #开启数据库的数量
save 900 1 # 创建本地数据库快照，格式：save * *  900秒内，执行1次写操作，300秒内，执行10次写操作，60秒内，执行10000次写操作
save 300 10
save 60 10000
rdbcompression yes #启用数据库lzf压缩，也可以设置为no
dbfilename dump.rdb  #本地快照数据库名称
dir "/usr/local/redis/var/"   #本地快照数据库存放目录
requirepass 123456  #设置redis数据库连接密码
maxclients 10000 #同一时间最大客户端连接数，0为无限制
maxmemory 1024MB #设定redis最大使用内存，值要小于物理内存，必须设置
appendonly yes  #开启日志记录，相当于MySQL的binlog
appendfilename "appendonly.aof"   #日志文件名，注意：不是目录路径
appendfsync everysec #每秒执行同步，还有两个参数always、no一般设置为everysec，相当于MySQL事物日志的
```

### 命令

- Key
	- DEL
	- KEYS
	- RANDOMKEY
	- EXISTS
	- EXPIRE
- String
	- SET/GET/APPEND
	- INCR/DECR
- Hash
	- HSET/HGET/HGETALL
	- HDEL/HEXISTS/HLEN
	- HKEYS/HVALS
- List
	- LPUSH/RPUSH
	- LPOP/RPOP
	- LLEN/LINDEX
- Set
	- SADD/SPOP
	- SMEMBERS
- Sorted Set
	- ZADD
	- ZCOUNT
	- ZRANGE
- Pub/Sub
- Transaction

[Redis完整命令](http://redis.readthedocs.org/en/2.6/)

批量删除key
redis-cli keys '*' | xargs redis-cli del

### 持久化

- 快照RDB
- 追加式文件AOF

[Redis的持久化](https://segmentfault.com/a/1190000002906345)详细介绍

### 一些常识

[关于Redis的一些常识](http://www.searchdatabase.com.cn/showcontent_70423.htm)