# ELK

## Elasticsearch

### 安装

`rpm --import http://packages.elasticsearch.org/GPG-KEY-elasticsearch`

```shell
cat > /etc/yum.repos.d/elasticsearch.repo <<EOF
[elasticsearch-2.x]
name=Elasticsearch repository for 2.x packages
baseurl=https://packages.elastic.co/elasticsearch/2.x/centos
gpgcheck=1
gpgkey=https://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
EOF
```

`yum install elasticsearch`即可

`chkconfig --add elasticsearch`添加开机启动

`service elasticsearch start|stop|restart|status` elasticsearch控制命令

如果遇到没有JAVA_HOME环境变量的问题，编辑`/etc/sysconfig/elasticsearch`, 添加JAVA_HOME

### 基本使用

以下示例中,database,table替换成自己实际的数据库与表，elasticsearch相对应的概念为index,type, `_id`表示某个文档的id

```shell
curl -X DELETE "localhost:9200/*"
curl -X GET "localhost:9200/_stats?pretty"
curl -X GET "localhost:9200/index/type/_search?q=tag:java"
curl -X GET "localhost:9200/index/type/_search?pretty" -d '{"query":{"match":{"tag":"java"}}}'
curl -X GET "localhost:9200/index/type/_id?pretty"
curl -X POST "localhost:9200/index/type?pretty" -d '{title: "java", tag: "java"}'
curl -X PUT "localhost:9200/index/type/_id?pretty" -d '{title: "groovy", tag: "groovy"}'
curl -X DELETE "localhost:9200/index/type/_id?pretty"
curl -X DELETE "localhost:9200/logstash-2013.03.*"
```

## Logstash

### 安装

[官方文档](https://www.elastic.co/guide/en/logstash/current/index.html)

```shell
cat > /etc/yum.repos.d/logstash.repo <<EOF
[logstash-2.3]
name=Logstash repository for 2.3.x packages
baseurl=http://packages.elastic.co/logstash/2.3/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
EOF
```

`yum install logstash`

`/opt/logstash/bin/logstash -f /etc/logstash/conf.d/logstash.conf`

### 基本使用

进入/opt/logstatsh目录，位于终端输入：

`bin/logstash -e input{stdin{}}filter{date{match=>["message","dd/MMM/yyyy:HH:mm:ss Z"]}}output{stdout{codec=>rubydebug}}'`

然后终端会等待你的输入。敲入 `04/May/2016:23:36:34 +0800`，回车。

watch_file位于`/var/lib/logstash` 或者 `$HOME/.since_db*`目录，重新测试读取文件需要删除since_db文件

配置文件存放位置 `/etc/logstash/conf.d/`

logstash的配置文件格式为

```shell
input{}
filter{}
output{}
```

log4j info 日志配置

```shell
filter {
    grok {
	  match => {
	      "message" => "%{LOGBACK_DATESTAMP:time} - %{DATA} %{LOGLEVEL:log_level}%{SPACE}%{JAVA_METHOD:clazz}%{DATA} - %{GREEDYDATA:msg}"
	  }
    }

    date {
	  match => ["time", "yyyy-MM-dd HH:mm:ss.SSS"]
    }
}
```

由于`LOGBACK_DATESTAMP`,与`JAVA_METHOD`不属于标准的logstash-patterns，需要自行在`/opt/logstash/vendor/bundle/jruby/1.9/gems/logstash-patterns-core-2.0.5/patterns`添加一个custom文件,添加以下内容

```shell
LOGBACK_DATESTAMP 20%{YEAR}-%{MONTHNUM}-%{MONTHDAY} %{HOUR}:?%{MINUTE}(?::?%{SECOND})
JAVA_METHOD %{JAVACLASS:clazz}\(%{NUMBER:line}\)
```

输入: `2016-05-05 00:45:23.360 - [tomcat-http--47] INFO  com.zz.Controller.trans(66) - 消息内容:消息`

输出:

```shell
{
       "message" => "2016-05-05 00:45:23.360 - [tomcat-http--47] INFO  com.zz.Controller.trans(66) - 消息内容:消息",
      "@version" => "1",
    "@timestamp" => "2016-05-05T04:45:23.360Z",
          "host" => "Master.Hadoop",
          "time" => "2016-05-05 00:45:23.360",
     "log_level" => "INFO",
         "clazz" => "com.zz.Controller.trans",
          "line" => "66",
           "msg" => "消息内容:消息"
}

```

logstash 的nginx配置示例,注意nginx的log_format需要改成`log_format logstash '$remote_addr|$remote_user|$time_local|$status|$request_time|$body_bytes_sent|$request|$http_referer|$http_user_agent|'`以便适应ruby filter的配置。

```shell
input {
    file {
        path => ["/var/log/nginx/logstash.access.log"]
        type => "nginx-logstash"
        sincedb_path => "/var/lib/logstash/.sincedb_nginx"
        start_position => "beginning"
    }
}

filter {
    ruby {
        init => "@kname = ['remote_addr','remote_user','time_local','status','request_time','body_bytes_sent','request','http_referer','http_user_agent']"
        code => "event.append(Hash[@kname.zip(event['message'].split('|'))])"
    }
    if [request] {
        ruby {
            init => "@kname = ['method','uri','verb']"
            code => "event.append(Hash[@kname.zip(event['request'].split(' '))])"
        }
    }
    mutate {
        convert => [
            "body_bytes_sent" , "integer",
            "request_time", "float"
        ]
    }
    date {
        match => [ "time_local", "dd/MMM/yyyy:HH:mm:ss Z" ]
        locale => "en"
    }
}

output {
    elasticsearch {
        hosts => ["192.168.152.10:9200"]
        index => "logstash-%{type}"
        document_type => "%{type}"
        workers => 1
        flush_size => 20000
        idle_flush_time => 10
        template_overwrite => true
    }
}
```

另外一个例子, logback例子
```xml
input {
    file {
        type => "zz-type"
        path => ["/opt/tomcat/logs/zz-*.log"]
	      sincedb_path => "/var/lib/logstash/.sincedb_zz"
        start_position => "beginning"
    }
}

filter {
    multiline {
        pattern => '^\['
        negate => true
        what => previous
    }

    grok {
        pattern => [
            "\[%{TIMESTAMP_ISO8601:timestamp}\] \[%{DATA:thread}\] %{LOGLEVEL:logLevel} %{DATA:class}\[%{DATA:line}\]\-> \[%{DATA:track}\] %{GREEDYDATA:msg}"
        ]
        remove_field => ["message"]
    }

    if "_grokparsefailure" in [tags] {
        grok {
            pattern => [
                "\[%{TIMESTAMP_ISO8601:timestamp}\] \[%{DATA:thread}\] %{LOGLEVEL:logLevel} %{DATA:class}\[%{DATA:line}\]\-> \[%{DATA:track}\] (?<msg>(.|\r|\n)*)"
            ]
        }
    }

    date {
        match => [
            "timestamp" , "YYYY-MM-dd HH:mm:ss.SSS"
        ]
        target => "@timestamp"
    }
}

output {
    elasticsearch {
        hosts => ["123.57.151.120:9200"]
        index => "logstash-%{type}"
        document_type => "%{type}"
        workers => 1
        flush_size => 20000
        idle_flush_time => 10
        template_overwrite => true
    }
}
```

### Kibana

### 安装

```shell
cat > /etc/yum.repos.d/kibana.repo <<EOF
[kibana-4.5]
name=Kibana repository for 4.5.x packages
baseurl=http://packages.elastic.co/kibana/4.5/centos
gpgcheck=1
gpgkey=http://packages.elastic.co/GPG-KEY-elasticsearch
enabled=1
EOF
```

`yum install kibana`

`chkconfig --add kibana`添加自启动

配置文件位于`/opt/kibana/config/kibana.yml`

`service kibana start|stop|restart|status` kibana控制命令

安装成功后打开`http://127.0.0.1:5601`

在Setting菜单中，创建`logstash-*` indices，然后在 `Discover` 菜单中可查看index中的日志。

查询语法：

- 使用双引号做短语查询 "username password"
- 字段
  - field:value  字段搜索
  - _exists_:field 返回结果中需要有field字段
  - _missing_:field 返回结果不能含有field字段
- 通配符 `?`匹配单个字符，`*`匹配0到多个字符
- 正则
- 范围搜索 `length:[100 TO 200]`
- 逻辑操作 `AND` `OR` +apache -jakarta test：结果中必须存在apache，不能有jakarta，test可有可无
- 转义特殊字符
