# Mysql

### 安装

- 卸载老版本mysql

查找系统已经安装的mysql并卸载之， `rpm -qa | grep mysql`， `yum remove -y mysql-libs`

- yum安装

添加rpm源
```shell
wget dev.mysql.com/get/mysql-community-release-el6-5.noarch.rpm
yum localinstall mysql-community-release-el6-5.noarch.rpm
```
安装5.6版本
```shell
yum-config-manager --disable mysql55-community
yum-config-manager --enable mysql56-community
yum-config-manager --disable mysql57-community
yum install –y mysql
```

- 添加开机启动
	- CentOS 6 `chkconfig mysqld on`
	- CentOS 7 `systemctl enable mysqld`
- mysql命令
	- CentOS 6 `service mysqld start|stop|restart|status`
	- CentOS 7 `systemctl start|stop|restart|status mysqld`

### 命令

- 修改root用户密码

登录mysql, `mysql -u root`

进入数据库后，使用命令：`set password for 'root'@'localhost'= PASSWORD("123456")`

或者`update user set password=password('123456') where user= 'root';`

- 设置可以远程连接

修改Host为%，然后`flush privileges`即可。
```shell
use mysql;
update user set Host='%', Password='' where User='root' and Host='127.0.0.1';
flush privileges;
```
注意sql语句中的Password及Host可以先使用`select Host,Password,User from user`语句查出来。

- 分析sql语句 `explain sql`
- 查看设置mysql变量 `show variables like '%slow%';`，`set global log_slow_queries=ON`

### 配置

- 慢查询日志

修改`/etc/my.cnf`
```shell
[mysqld]
slow_query_log=1  #启用慢查询
slow_query_log_file=/var/mysql/slow.log #慢查询日志
long_query_time=2 # 慢查询时间2s
log-queries-not-using-indexes=1 #记录没有使用索引的查询
```

- 最大连接数

```shell
[mysqld]
max_connections=1000
```

- 编码

```shell
[mysqld]
default_character_set=utf8
character-set-server=utf8
collation-server=utf8_general_ci
```

### 规范

[设计规范](http://git.oschina.net/yxb_1990/program-name-rules/blob/master/Sql.md)

### 集群

- 主从复制

配置Master：

```shell
[mysqld]
log-bin=mysql-bin   //[必须]启用二进制日志
server-id=10       //[必须]服务器唯一ID，默认是1，一般取IP最后一段
```

重启mysql，`service mysqld restart`

对slave授权，`GRANT REPLICATION SLAVE ON *.* to 'root'@'%' identified by 'password';`

查看master状态，`show master status`， 并记录FILE 及 Position 的值，在后面进行从服务器操作的时候需要用到。

配置Slave：

```shell
[mysqld]
log-bin=mysql-bin   //[非必须]启用二进制日志
server-id=11       //[必须]服务器唯一ID，默认是1，一般取IP最后一段
```

重启mysql，`service mysqld restart`

配置Master，`change master to master_host='192.168.152.10',master_user='root', master_password='123456', master_log_file='mysql-bin.000005', master_log_pos=261;`

开启Slave，`start slave;`

检查同步状态，`show slave status\G`

```shell
Slave_IO_Running: YES
Slave_SQL_Running: YES
```
当以上两个选项都为YES，表示同步成功。

注意：如果主服务器已经存在应用数据，则在进行主从复制时，需要做以下处理：

主数据库进行锁表操作，不让数据再进行写入动作`FLUSH TABLES WITH READ LOCK;`

查看主数据库状态，`show master status;`

记录下 FILE 及 Position 的值。将主服务器的数据文件（整个/var/lib/mysql目录）复制到从服务器

取消主数据库锁定，`UNLOCK TABLES;`

主从配置参数含义
```shell
server-id=1   #值唯一的标识了复制群集中的主从服务器，因此它们必须各不相同。master_id必须为1到65535之间的一个正整数值，slave_id值必须为2到65535之间的一个正整数值。
log-bin=mysql-bin       #表示打开binlog,打开该选项才可以通过I/O写到Slave的relay-log,也是可以进行replication的前提;
binlog-do-db=mysql,account		#表示需要记录进制日志的数据库。如果有多个数据库可用逗号分隔，或者使用多个binlog-do-db选项
binlog-ignore-db=mysql		#表示不需要记录二进制日志的数据库。如果有多个数据库可用逗号分隔，或者使用多个binlog-do-db选项
replicate-do-db=mysql 		#表示需要同步的数据库，如果有多个数据库可用逗号分隔，或者使用多个replicate-do-db选项
replicate-ignore-db=mysql		#表示不需要同步的数据库，如果有多个数据库可用逗号分隔，或者使用多个replicate-ignore-db=mysql选项
auto_increment_offset=1
auto_increment_increment=2		#auto_increment_increment和auto_increment_offset用于主－主服务器(master-to-master)复制，并可以用来控制AUTO_INCREMENT列的操作
expire-logs-days=100 #删除过期日志，100天
```

- 主主复制

### 优化

1. 优化sql和索引
2. 加缓存，memcached,redis
3. 主从复制或主主复制，读写分离
4. mysql分区（慎用分区，往往OLTP操作不适用分区，分区反倒会拖慢原有查询）
5. 垂直拆分（分库），根据你模块的耦合度，将一个大的系统分为多个小的系统
6. 水平拆分（分表），针对数据量大的表，要选择一个合理的sharding key,为了有好的查询效率，表结构也要改动，做一定的冗余，应用也要改，sql中尽量带sharding key，将数据定位到限定的表上去查，而不是扫描全部的表

mysql数据库一般都是按照这个步骤去演化的，成本由低到高

### 管理

- 备份
	- 备份命令格式 `mysqldump -h主机名  -P端口 -u用户名 -p密码 –database 数据库名 > 文件名.sql`
	- 备份所有数据库 `mysqldump –all-databases > allbackupfile.sql`
	- 备份多个数据库 `mysqldump –databases databasename1 databasename2 databasename3 > multibackupfile.sql`
	- 备份数据库结构 `mysqldump –no-data –databases databasename1 databasename2 databasename3 > structurebackupfile.sql`
	- 备份数据库 `mysqldump discuz > discuz.sql`
	- 备份表 `mysqldump discuz user > user.sql`

- 还原
	- 还原表 `mysqldump -uroot -p123456 discuz < user.sql`
	- 导入数据 `source /var/backup/user.sql`