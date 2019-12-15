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

`mongo --host 127.0.0.1  -u "root" -p "123456"  --authenticationDatabase admin`

进入mongo shell后输入help 命令：即可显示所有帮助命令

```shell
db.help()                    #help on db methods
db.mycoll.help()             #help on collection methods
sh.help()                    #sharding helpers
rs.help()                    #replica set helpers
help admin                   #administrative help
help connect                 #connecting to a db help
help keys                    #key shortcuts
help misc                    #misc things to know
help mr                      #mapreduce
                             
show dbs                     #show database names
show collections             #show collections in current database
show users                   #show users in current database
show profile                 #show most recent system.profile entries with time >= 1ms
show logs                    #show the accessible logger names
show log [name]              #prints out the last segment of log in memory, 'global' is default
use <db_name>                #set current database
db.foo.find()                #list objects in collection foo
db.foo.find( { a : 1 } )     #list objects in foo where a == 1
db.foo.find({a:1, b:{$gt:0}}).skip(10).limit(10).count()
it                           #result of the last line evaluated; use to further iterate
DBQuery.shellBatchSize = x   #set default number of items to display on shell
exit                         #quit the mongo shell
```

### MapReduce

文档内容如下：
```json
{
     _id: ObjectId("50a8240b927d5d8b5891743c"),
     cust_id: "abc123",
     ord_date: new Date("Oct 04, 2012"),
     status: 'A',
     price: 25,
     items: [ { sku: "mmm", qty: 5, price: 2.5 },
              { sku: "nnn", qty: 5, price: 2.5 } ]
}
```

计算每个客户总消费值
```javascript
var mapFunction1 = function() {
	emit(this.cust_id, this.price);
};

var reduceFunction1 = function(keyCustId, valuesPrices) {
	return Array.sum(valuesPrices);
};

db.orders.mapReduce(
	mapFunction1,
	reduceFunction1,
	{ out: "map_reduce_example" }
)
```

复杂一点的例子：计算订单总数及总item.qty总数量，以及item.qty平均数量

```javascript
var mapFunction2 = function() {
	for (var idx = 0; idx < this.items.length; idx++) {
		var key = this.items[idx].sku;
		var value = {
			count: 1,
			qty: this.items[idx].qty
		};
		emit(key, value);
	}
};

var reduceFunction2 = function(keySKU, countObjVals) {
	reducedVal = { count: 0, qty: 0 };

	for (var idx = 0; idx < countObjVals.length; idx++) {
		reducedVal.count += countObjVals[idx].count;
		reducedVal.qty += countObjVals[idx].qty;
	}

	return reducedVal;
};

var finalizeFunction2 = function(key, reducedVal) {
	reducedVal.avg = reducedVal.qty/reducedVal.count;

	return reducedVal;
};

db.orders.mapReduce(mapFunction2, reduceFunction2,
	{
		out: { merge: "map_reduce_example" },
		query: { ord_date: { $gt: new Date('01/01/2012') } },
		finalize: finalizeFunction2
	}
)
```

### 副本集

### 管理

- 备份

`mongodump` 可以备份整个服务器、单个database或者单个collection的数据，也可以通过查询语句只备份collection中的部分数据

`mongodump -h <host> -u <username> -p <password> -d <database> -c <collection> -q <json> -o <path> --archive <file or null> --gzip`

注意`--archive`, `--gzip`是3.2版本的新参数，可以压缩导出的数据，并可以通过`mongorestore`还原，`--archve`与`-o` 参数不能同时使用

例子:

`mongodump -h 192.168.152.10 -u root -p 123456 -d zz -c user -q "{username:'yxb_1990'}" -o /var/backup`

- 恢复

`mongorestore` 既可以恢复整个备份也可以恢复一部分

`mongorestore -h -u -p -d <database> -c <collection> --archive <file or null> --gzip --drop <path>`

`--drop` 参数会删除再还原，慎用

例子:

`mongorestore -h 192.168.152.10 -u root -p 123456 -d zz -c user /var/back/zz/user.bson`

- 查看mongo状态

`mongostat -h -u -p`
