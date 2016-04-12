##open-falcon

### 安装

参考官网[wiki](http://book.open-falcon.org/zh/quick_install/prepare.html)

### 基本使用

即[安装绘图组件](http://book.open-falcon.org/zh/quick_install/graph_components.html)，使用到的模块有`agent`，`transfer`，`graph`，`query`，`dashboard`，过程为`agent`采集数据传输到`transfer`, `transfer`转发给`graph`，`query`从`graph`读取数据, `dashboard`从`query`查询数据展示给用户看。

如果一切顺利，打开浏览器访问[agent](http://127.0.0.1:1988) 端口为1988, [dashboard](http://127.0.0.1:8081) 端口为8081。

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
                "dsn": "root:123456@tcp(192.168.152.10:3306)/graph?loc=Local&parseTime=true", // 数据库连接
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
DASHBOARD_DB_PASSWD = "123456"
DASHBOARD_DB_NAME = "dashboard"

#-- graph db config --
GRAPH_DB_HOST = "192.168.152.10"
GRAPH_DB_PORT = 3306
GRAPH_DB_USER = "root"
GRAPH_DB_PASSWD = "123456"
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