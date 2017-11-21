## 使用filebeat,elasticsearch,grafana 监控nginx日志

### 目标

1. qps, 响应时间, 最大响应时间 (特定接口)
2. pv,uv(特定接口)
3. 终端统计
4. 状态码统计，非200的比较多的接口
5. 访问慢的接口


### elasticsearch

安装插件

`sudo elasticsearch-plugin install ingest-geoip`
`sudo elasticsearch-plugin install ingest-user-agent`

重启elasticsearch

### 安装filebeat v5.3.2

配置：filebeat 记录处理日志的文件/var/filebeat/registry

`vi filebeat.yml` 添加内容

```yml
filebeat.modules:
- module: nginx
  access:
    var.paths: ["/var/log/nginx/*.log"]
```

### grafana

配置elasticsearch 数据源，新建一个dashboard

1. pv

选择singlestat, 修改Interval 为`1d`

2. qps

选择Graph, 添加Group By, Terms, 选择字段`nginx.access.url`

3. 状态码统计

选择Graph, 添加Group By, Terms, 选择字段`nginx.access.response_code`

4. 404状态码统计

选择Graph, 设置Query：`nginx.access.response_code: 404` 添加Group By, Terms, 选择字段`nginx.access.response_code`, 设置Min Doc Count 大于0