# Docker

## 安装

系统要求是64 位操作系统，内核版本至少为 3.10，Docker 目前支持 CentOS 6.5 及以后的版本，推荐使用 CentOS 7 系统。

使用`uname -r`查看内核版本

- 脚本安装

`curl -fsSL https://get.docker.com/ | sh`

- 添加自启动 `chkconfig docker on`
- 启动关闭命令 `service docker start|stop|restart|status`

## 命令

- 镜像

```shell
docker images #列出本地镜像
docker search ubuntu #搜索ubuntu镜像
docker pull ubuntu #下载ubuntu镜像
docker commit #镜像修改后提交
docker save -o ubuntu_14.04.tar ubuntu:14.04 #存储
docker load --input ubuntu_14.04.tar #载入
docker rmi ubuntu:14:04 #删除镜像
```

- 容器

```shell
docker run --rm -t -i ubuntu /bin/bash #运行完成后自动删除容器
docker run --name mysql -p 3307:3306 -v /home/webapps/mysql/data/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql # 后台运行mysql实例，-p 端口映射，从外部3307映射容器的3306，-v 数据卷映射，-e环境变量设置
docker ps [-a] #显示运行的容器实例，-a 参数表示所有的，包括已经停止运行的容器
docker start|stop|restart container  
docker exec -ti container /bin/bash #进入容器打开bash
docker rm container #删除容器
docker rm $(docker ps -a -q) #删除所有停止运行的容器
docker logs -f container #查看容器日志
```

## Dockfile

```shell

FROM java:8

MAINTAINER Mr.Yang "paul.wide@gmail.com"

RUN apt-get install -y openssh-server
RUN mkdir -p /var/run/sshd
# 设置root ssh远程登录密码为123456
RUN echo "root:123456" | chpasswd

EXPOSE 22

VOLUME /tmp

ADD springboot-0.1.jar app.jar

ENTRYPOINT ["java","-jar","/app.jar"]
```

其中，springboot-0.1.jar 跟dockfile文件位于同一目录下，然后执行`docker build -t springboot:0.1 .`

## 管理
