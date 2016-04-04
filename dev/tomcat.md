# Tomcat

### 安装

先安装Java，参考[Java](http://git.oschina.net/yxb_1990/DevOps/blob/master/dev/java.md)

从官网下载[tomcat8.0.33](http://mirrors.cnnic.cn/apache/tomcat/tomcat-8/v8.0.33/bin/apache-tomcat-8.0.33.tar.gz), 解压`tar -zxvf  apache-tomcat-8.0.33.tar.gz -C /usr/local/`至`/usr/local/`

启动，停止命令。运行`/usr/local/tomcat8/bin/catalina.sh start`,`/usr/local/tomcat8/bin/catalina.sh stop`

开机自启动 `echo /usr/local/tomcat8/bin/catalina.sh start >> /etc/rc.local`

### 配置

- 设置UTF-8编码

修改`server.xml` Connector节点，添加`URIEncoding="UTF-8"`

- 修改监听端口

修改`server.xml` Connector 节点的port属性，默认为8080

- 设置运行模式

修改`server.xml` Connector 节点，添加`protocol="org.apache.coyote.http11.Http11NioProtocol"`

- 设置连接数

```xml
<Connector port="8080"
maxThreads="150"
minSpareThreads="25"
maxSpareThreads="75"
acceptCount="100" />
```

maxThreads="150" 表示最多同时处理150个连接

minSpareThreads="25" 表示即使没有人使用也开这么多空线程等待

maxSpareThreads="75" 表示如果最多可以空75个线程，例如某时刻有80人访问，之后没有人访问了，则tomcat不会保留80个空线程，而是关闭5个空的。

acceptCount="100" 当同时连接的人数达到maxThreads时，还可以接收排队的连接，超过这个连接的则直接返回拒绝连接。

- 设置JVM参数

在`TOMCAT_HOME/bin` 目录下新建`setenv.sh`, 设置jvm参数

`JAVA_OPTS="-server -Xms1024m -Xmx1024m -Xss256K -XX:PermSize=64m -XX:MaxPermSize=128m"`