# Mysql

### 安装

- 卸载老版本mysql

查找系统已经安装的mysql并卸载
`rpm -qa | grep mysql`

`yum remove -y mysql-libs`

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

进入数据库后，使用命令：`set password for 'root'@'localhost'= PASSWORD("123456")`，或者`update user set password=password('123456') where user= 'root';`

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

- 慢日志

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

### 优化

### 管理

- 备份
	- 备份数据库 `mysqldump -uroot -p123456 discuz > discuz.sql`
	- 备份表 `mysqldump -uroot -p123456 discuz user > user.sql`

- 还原
	- 还原表 `mysqldump -uroot -p123456 discuz < user.sql`