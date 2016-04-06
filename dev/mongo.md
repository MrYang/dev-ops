# Mongo

### 安装（基于3.2版本）

新建 `/etc/yum.repo.d/mongodb-enterprise.repo`文件，输入

```shell
[mongodb-enterprise]
name=MongoDB Enterprise Repository
baseurl=https://repo.mongodb.com/yum/redhat/$releasever/mongodb-enterprise/stable/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

然后运行命令`yum install -y mongodb-enterprise`即可安装

添加自启动 `chkconfig mongod on`

mongo启动命令 `service mongod start|stop|restart|status`

日志及数据默认存储在`/var/log/mongodb`,`/var/lib/mongo`

### 命令

### MapReduce

### 副本集

### 管理