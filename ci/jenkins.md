# Jenkins

### 安装

从官网下载jenkins.war 扔进tomcat，启动即可

- docker 安装

`docker pull jenkins`

`docker run --name jenkins -v /var/jenkins_home -p 50000:50000 -p 8080:8080 -d jenkins` 启动

### 配置

添加maven，gradle，Subversion，git插件

- gradle+git项目配置
