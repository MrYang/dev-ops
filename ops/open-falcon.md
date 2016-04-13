##open-falcon

### 安装

参考官网[wiki](http://book.open-falcon.org/zh/quick_install/prepare.html)

### 基本使用

即[安装绘图组件](http://book.open-falcon.org/zh/quick_install/graph_components.html)，使用到的模块有`agent`，`transfer`，`graph`，`query`，`dashboard`

过程为`agent`采集数据传输到`transfer`, `transfer`转发给`graph`，`query`从`graph`读取数据, `dashboard`从`query`查询数据展示给用户看。

如果一切顺利，打开浏览器访问[agent](http://127.0.0.1:1988) 数据，地址为`http://127.0.0.1:1988`。可以监控硬盘，cpu，内存，负载等基本数据。

[dashboard](http://127.0.0.1:8081) 后台，地址为`http://127.0.0.1:8081`。以图形化的形式查看各个主机的监控项。

其中`Endpoint`表示一台linux服务器，通常为`hostname`, `Counter`为监控项

- agent 主要配置

```json
{
	"heartbeat": {		//心跳，后面会使用到
        "enabled": true,  //是否启用
        "addr": "127.0.0.1:6030",
        "interval": 60,
        "timeout": 1000
    },
    "transfer": {
        "enabled": true,
        "addrs": [
            "127.0.0.1:8433", // transfer监听地址，因为安装是在本地，所以可以是127.0.0.1
			"127.0.0.1:8433"
        ],
        "interval": 60,
        "timeout": 1000
    }
}
```

- transfer 主要配置

```json
{
	"judge": {  //告警判断，后面用到
        "enabled": true,
        "batch": 200,
        "connTimeout": 1000,
        "cluster": {
            "judge-00" : "127.0.0.1:6080"  // judge 监听地址
        }
    },  
    "graph": {  // 绘图组件
        "enabled": true,
        "replicas": 500,
        "cluster": {
            "graph-00" : "127.0.0.1:6070"  // graph监听地址
        }
    }
}
```

- graph 主要配置

```json
{
	"http": {
                "enabled": true,
                "listen": "0.0.0.0:6071"
        },
        "rpc": {
                "enabled": true,
                "listen": "0.0.0.0:6070"	// graph监听地址
        },
        "rrd": {
                "storage": "/home/work/data/6070"
        },
        "db": {
                "dsn": "root:111111@tcp(192.168.152.10:3306)/graph?loc=Local&parseTime=true", // 数据库连接
                "maxIdle": 4
        },
        "callTimeout": 5000,
        "migrate": {
                "enabled": false,
                "concurrency": 2,
                "replicas": 500,
                "cluster": {
                        "graph-00" : "127.0.0.1:6070" // 集群,分片
                }
        }
}
```

- query 主要配置

```json
{
	"graph": {
        "connTimeout": 1000,
        "callTimeout": 5000,
        "cluster": {
            "graph-00": "127.0.0.1:6070"  // graph地址
        }
    },
    "api": {
        "query": "http://127.0.0.1:9966",  // query监听地址，dashboard请求访问的地址
        "dashboard": "http://127.0.0.1:8081", //dashboard地址
        "max": 500
    }
}
```

- dashboard 配置

注意，该程序是python程序，配置文件为`rrd/config.py`

```python
DASHBOARD_DB_HOST = "192.168.152.10"
DASHBOARD_DB_PORT = 3306
DASHBOARD_DB_USER = "root"
DASHBOARD_DB_PASSWD = "111111"
DASHBOARD_DB_NAME = "dashboard"

#-- graph db config --
GRAPH_DB_HOST = "192.168.152.10"
GRAPH_DB_PORT = 3306
GRAPH_DB_USER = "root"
GRAPH_DB_PASSWD = "111111"
GRAPH_DB_NAME = "graph"

#-- app config --
DEBUG = True
SECRET_KEY = "secret-key"
SESSION_COOKIE_NAME = "open-falcon"
PERMANENT_SESSION_LIFETIME = 3600 * 24 * 30
SITE_COOKIE = "open-falcon-ck"

#-- query config --
QUERY_ADDR = "http://127.0.0.1:9966" # query请求地址

BASE_DIR = "/home/work/open-falcon/dashboard/"
```

### 告警

[安装告警组件](http://book.open-falcon.org/zh/quick_install/judge_components.html)，使用到的组件有`sender`，`fe`(frond end)，`portal`，`hbs`，`judge`， `alarm`

如果一切顺利，打开浏览器访问[fe](http://127.0.0.1:1234), 地址为`http://127.0.0.1:1234`，通过`http://127.0.0.1:1234/root?password=111111` 设置root用户密码。

[portal](http://127.0.0.1:5050),地址为`http://127.0.0.1:5050`，访问portal之前，需要先配置好fe。

- fe配置

```json
{
    "log": "debug",
    "http": {
        "enabled": true,
        "listen": "192.168.152.10:1234"  // 监听地址
    },
    "cache": {
        "enabled": true,
        "redis": "192.168.152.10:6379",
        "timeout": {
            "conn": 10000,
        }
    },
    "uic": {
        "addr": "root:111111@tcp(192.168.152.10:3306)/uic?charset=utf8&loc=Asia%2FChongqing",  // 数据库配置
        "idle": 10,
        "max": 100
    },
    "shortcut": {
        "falconPortal": "http://192.168.152.10:5050/",  // portal地址
        "falconDashboard": "http://192.168.152.10.8081/",  // dashboard地址
        "falconAlarm": "http://192.168.152.10:9912/"  // alarm地址
    }
}
```

- portal 配置

注意，该程序是python程序，配置文件为`frame/config.py`

```python
# -- app config --
DEBUG = True

# -- db config --
DB_HOST = "192.168.152.10"
DB_PORT = 3306
DB_USER = "root"
DB_PASS = "111111"
DB_NAME = "falcon_portal"

# -- cookie config --
SECRET_KEY = "4e.5tyg8-u9ioj"
SESSION_COOKIE_NAME = "falcon-portal"
PERMANENT_SESSION_LIFETIME = 3600 * 24 * 30

UIC_ADDRESS = {
    'internal': 'http://192.168.152.10:1234',  #fe地址
    'external': 'http://192.168.152.10:1234',
}
```

- hbs配置

```json
{
    "debug": true,
    "database": "root:111111@tcp(192.168.152.10:3306)/falcon_portal?loc=Local&parseTime=true", // 数据库连接地址
    "hosts": "",
    "maxIdle": 100,
    "listen": ":6030",
    "trustable": [""],
    "http": {
        "enabled": true,
        "listen": "0.0.0.0:6031"  //监听地址
    }
}
```

- sender配置

```json
{
    "debug": true,
    "http": {
        "enabled": true,
        "listen": "0.0.0.0:6066"  //监听端口
    },
    "redis": {
        "addr": "192.168.152.10:6379",
        "maxIdle": 5
    },
    "queue": {
        "sms": "/sms",  // redis队列名称
        "mail": "/mail"
    },
    "worker": {
        "sms": 10,
        "mail": 50
    },
    "api": {
        "sms": "http://192.168.152.10:8000/sms",   // 发送短信http接口
        "mail": "http://192.168.152.10:9000/mail" // 发送邮件http接口
    }
}
```

- alarm 配置
- judge 配置

### 自定义监控

- 端口告警

从`fe`，点击链接进入`portal`，输入机器组名称，添加`HostGroup`，点击`hosts`链接，添加host，注意添加的host要是机器的名字，即falcon_portal数据库host表中`hostname`字段的值。

点击`Templates`链接，添加一个模板，可以是跟`HostGroup`名称一样，添加策略，如 metric:`net.port.listen`, tags:`port=80`, note:`nginx80端口未开`,其他保持默认并保存，再在下面的告警设置中配置一个用户组，保存。

回到`HostGroup`，将刚刚创建的`hostgroup`与`template`绑定，即完成了80端口的告警。

其中：

|  metric/tags/note | condition  | max  | P  |
| ------------ | ------------ | ------------ | ------------ |
| net.port.listen/port=80  | all(#3)==0  | 3  | 0  |

表示监控80端口，如果三次返回的值都为0，则发送报警，max: 最大报警次数 P：报警级别（<3: 既发短信也发邮件 >=3: 只发邮件）