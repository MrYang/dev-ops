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

### 基本使用

以下示例中,database,table替换成自己实际的数据库与表，elasticsearch相对应的概念为index,type

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

### 基本使用

进入/opt/logstatsh目录，位于终端输入：

`bin/logstash -e 'input{stdin{}}output{stdout{codec=>rubydebug}}'`

然后终端会等待你的输入。敲入 Hello World，回车。

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

`service kibanastart|stop|restart|status` kibana控制命令

安装成功后打开`http://127.0.0.1:5601`