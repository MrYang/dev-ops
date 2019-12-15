# 基于nginx 日志的监控系统

目标

1. qps, 响应时间, 最大响应时间 (特定接口)
2. pv,uv(特定接口)
3. 终端统计
4. 状态码统计，非200的比较多的接口
5. 访问慢的接口

## 使用filebeat (v5.3.2) nginx 模块

### elasticsearch

安装插件

`sudo elasticsearch-plugin install ingest-geoip`
`sudo elasticsearch-plugin install ingest-user-agent`

重启elasticsearch

### 配置filebeat

配置：filebeat 记录处理日志的文件/var/filebeat/registry

`vi filebeat.yml` 添加内容

```yml
filebeat.modules:
- module: nginx
  access:
    var.paths: ["/var/log/nginx/*.log"]
```


## 使用filebeat,logstash,elasticsearch

### 配置nginx access log 

log_format filebeat $time_local | $remote_addr | $http_host | $http_x_forwarded_for | $request_method | $request_uri | $server_protocol | $status | $body_bytes_sent | $http_referer | $http_user_agent | $request_time|;

设置server的 access_log filebeat

### 配置filebeat

设置output 为logstash,注释掉 out.elasticsearch

```yml
filebeat.prospectors:
- input_type: log
  paths:
    - /data/weblog/nginx/logs/server_access.log
  tags: ["nginx-accesslog"]
  document_type: nginxaccess
output.logstash:
  hosts: ["127.0.0.1:5044"]
```

### 配置logstash

参考[open-faclon](https://github.com/MrYang/dev-ops/blob/master/ops/elk.md)

```javascript
input {
  beats {
    port => 5044
  }
}

filter {
  if "nginx-accesslog" in [tags] {
    grok {
      match => { "message" => "%{HTTPDATE:timestamp}\|%{IP:remote_addr}\|%{IPORHOST:http_host}\|(?:%{DATA:http_x_forwarded_for}|-)\|%{DATA:request_method}\|%{DATA:request_uri}\|%{DATA:server_protocol}\|%{NUMBER:status}\|(?:%{NUMBER:body_bytes_sent}|-)\|(?:%{DATA:http_referer}|-)\|%{DATA:http_user_agent}\|(?:%{DATA:request_time}|-)\|"}
      remove_field => ["message","server_protocol"]
    }
    if "_grokparsefailure" in [tags] {
      drop { }
    }
    mutate {
      convert => ["status","integer"]
      convert => ["body_bytes_sent","integer"]
      convert => ["request_time","float"]
    }
    geoip {
      source=>"remote_addr"
    }
    date {
      match => [ "timestamp","dd/MMM/YYYY:HH:mm:ss Z"]
      target => "@timestamp"
    }
  }
}

output {
  elasticsearch {
    hosts => ["127.0.0.1:9200"]
    index => "logstash-%{type}-%{+YYYY.MM.dd}"
    document_type => "%{type}"
  }
}
```

grafana 展示

配置elasticsearch 数据源，新建一个dashboard

1. pv

选择singlestat, 修改Interval 为`1d`

2. qps

选择Graph, 添加Group By, Terms, 选择字段`nginx.access.url`

3. 状态码统计

选择Graph, 添加Group By, Terms, 选择字段`nginx.access.response_code`

4. 404状态码统计

选择Graph, 设置Query：`nginx.access.response_code: 404` 添加Group By, Terms, 选择字段`nginx.access.response_code`, 设置Min Doc Count 大于0