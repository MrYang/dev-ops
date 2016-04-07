# SonarQube

### 安装

从官网下载并解压 `unzip sonarqube-5.1.1.zip -d /usr/local` 到`/usr/local`目录

使用mysql创建sonar数据库 `CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci;`

编辑`conf/sonar.properties`，设置mysql参数

```ini
sonar.jdbc.username=root
sonar.jdbc.password=123456
sonar.jdbc.url=jdbc:mysql://192.168.152.10:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true
sonar.jdbc.driverClassName=com.mysql.jdbc.Driver
```

启动sonarqube `/usr/local/sonarqube/bin/linux-x86-64/sonar.sh start`

如果遇到ruby没有找到gem什么的错误，可以先执行命令`rvm use system`，再启动

添加开机启动 `echo /usr/local/sonarqube/bin/linux-x86-64/sonar.sh start >> /etc/rc.local`

### 与Maven配合

编辑maven `setting.xml`文件，在`profiles`节点添加内容

```xml
<profile>
   <id>sonar</id>
   <activation>
	  <activeByDefault>true</activeByDefault>
   </activation>
   <properties>
	  <sonar.jdbc.url>jdbc:mysql://192.168.152.10:3306/sonar</sonar.jdbc.url>
	  <sonar.jdbc.driver>com.mysql.jdbc.Driver</sonar.jdbc.driver>
	  <sonar.jdbc.username>root</sonar.jdbc.username>
	  <sonar.jdbc.password>123456</sonar.jdbc.password>
	  <sonar.host.url>http://192.168.152.10:9000</sonar.host.url>
   </properties>
</profile>
```

执行`mvn sonar:sonar`即可查看分析结果