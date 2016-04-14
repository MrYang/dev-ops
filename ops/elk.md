# ELK

## Logstash

### 安装

安装2.3版本，如果需要安装其他版本，可以按照[官方文档](https://www.elastic.co/guide/en/logstash/current/index.html)配置其他版本

`rpm --import http://packages.elasticsearch.org/GPG-KEY-elasticsearch`

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

`yum clean all && yum install logstash`

### 基本使用

进入/opt/logstatsh目录，位于终端输入：

`bin/logstash -e 'input{stdin{}}output{stdout{codec=>rubydebug}}'`

然后终端会等待你的输入。敲入 Hello World，回车。