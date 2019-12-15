# nginx

### 安装

- yum 方式安装
	- 安装 EPEL 仓库 `yum install epel-release`

        如果系统不能安装epel-release，从`http://dl.fedoraproject.org/pub/epel/`下载系统对应的epel-release.rpm, 安装之, `rpm -Uvh epel-release.rpm`

	- 安装nginx `yum install nginx`
	- 开机启动
		- CentOS 6 `chkconfig nginx on`
		- CentOS 7 `systemctl enable nginx`
	- nginx命令
		- CentOS 6 `service nginx start|stop|restart|status`
		- CentOS 7 `systemctl start|stop|restart|status nginx`

- 源码安装

从官网下载nginx，解压`tar -zxvf nginx.tar.gz`， 进入解压目录执行命令
```shell
./configure
make
make install
```
configure命令是用来检测你的安装平台的目标特征的。它定义了系统的各个方面，包括nginx的被允许使用的连接处理的方法，比如它会检测你是不是有CC或GCC，并不是需要CC或GCC，它是个shell脚本，执行结束时，它会创建一个Makefile文件。

make是用来编译的，它从Makefile中读取指令，然后编译。

make install是用来安装的，它也从Makefile中读取指令，安装到指定的位置。

安装编译过程中如果出现错误，可以从错误信息中安装gcc,open-ssl等依赖库。

安装时的配置参数主要有：

–prefix=path 安装位置， 默认为 `/usr/local/nginx`

–sbin-path=path 设置可执行文件路径， 默认为`prefix/sbin/nginx`

–conf-path=path 设置配置文件路径，默认为`prefix/conf/nginx.conf`

–pid-path=path 设置pid路径，默认为`prefix/logs/nginx.pid`

一些可选模块：

–without-http_gzip_module

–without-http_rewrite_module

–without-http_proxy_module

–with-http_ssl_module

–with-pcre=path

-with-zlib=path

示例：

```shell
./configure
    --prefix=/usr/local/nginx
    --sbin-path=/usr/local/nginx/nginx
    --conf-path=/usr/local/nginx/nginx.conf
    --pid-path=/usr/local/nginx/nginx.pid
    --with-http_ssl_module
    --with-pcre=../pcre-4.4
    --with-zlib=../zlib-1.1.3
```

nginx 命令, `nginx -s start|stop|restart`

为什么要使用源码安装？

可以添加自定义参数，自定义安装模块，方便添加第三方模块

### 配置

- 完整nginx.conf配置

```conf
user  www www;
worker_processes  2;
pid /var/run/nginx.pid;

# [ debug | info | notice | warn | error | crit ]
error_log  /var/log/nginx.error_log  info;

events {
  worker_connections   2000;
  # use [ kqueue | rtsig | epoll | /dev/poll | select | poll ] ;
  use kqueue;
}

http {
  include       conf/mime.types;
  default_type  application/octet-stream;

  log_format main      '$remote_addr - $remote_user [$time_local]  '
    '"$request" $status $bytes_sent '
    '"$http_referer" "$http_user_agent" '
    '"$gzip_ratio"';

  log_format download  '$remote_addr - $remote_user [$time_local]  '
    '"$request" $status $bytes_sent '
    '"$http_referer" "$http_user_agent" '
    '"$http_range" "$sent_http_content_range"';

  client_header_timeout  3m;
  client_body_timeout    3m;
  send_timeout           3m;

  client_header_buffer_size    1k;
  large_client_header_buffers  4 4k;

  gzip on;
  gzip_min_length  1100;
  gzip_buffers     4 8k;
  gzip_types       text/plain;

  output_buffers   1 32k;
  postpone_output  1460;

  sendfile         on;
  tcp_nopush       on;

  tcp_nodelay      on;
  send_lowat       12000;

  keepalive_timeout  75 20;

  # lingering_time     30;
  # lingering_timeout  10;
  # reset_timedout_connection  on;


  server {
    listen        one.example.com;
    server_name   one.example.com  www.one.example.com;

    access_log   /var/log/nginx.access_log  main;

    location / {
      proxy_pass         http://127.0.0.1/;
      proxy_redirect     off;

      proxy_set_header   Host             $host;
      proxy_set_header   X-Real-IP        $remote_addr;
      # proxy_set_header  X-Forwarded-For  $proxy_add_x_forwarded_for;

      client_max_body_size       10m;
      client_body_buffer_size    128k;

      client_body_temp_path      /var/nginx/client_body_temp;

      proxy_connect_timeout      90;
      proxy_send_timeout         90;
      proxy_read_timeout         90;
      proxy_send_lowat           12000;

      proxy_buffer_size          4k;
      proxy_buffers              4 32k;
      proxy_busy_buffers_size    64k;
      proxy_temp_file_write_size 64k;

      proxy_temp_path            /var/nginx/proxy_temp;

      charset  koi8-r;
    }

    error_page  404  /404.html;

    location /404.html {
      root  /spool/www;

      charset         on;
      source_charset  koi8-r;
    }

    location /old_stuff/ {
      rewrite   ^/old_stuff/(.*)$  /new_stuff/$1  permanent;
    }

    location /download/ {
      valid_referers  none  blocked  server_names  *.example.com;

      if ($invalid_referer) {
        #rewrite   ^/   http://www.example.com/;
        return   403;
      }

      # rewrite_log  on;
      # rewrite /download/*/mp3/*.any_ext to /download/*/mp3/*.mp3
      rewrite ^/(download/.*)/mp3/(.*)\..*$ /$1/mp3/$2.mp3 break;

      root         /spool/www;
      # autoindex    on;
      access_log   /var/log/nginx-download.access_log  download;
    }

    location ~* ^.+\.(jpg|jpeg|gif)$ {
      root         /spool/www;
      access_log   off;
      expires      30d;
    }
  }
}
```

- 另外一个完整示例

[nginx.conf](https://www.nginx.com/resources/wiki/start/topics/examples/full/)

- 负载均衡

```shell
http {
  upstream myproject {
    server 127.0.0.1:8000 weight=3;
    server 127.0.0.1:8001;
    server 127.0.0.1:8002;
    server 127.0.0.1:8003;
  }

  server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }
}
```

- 日志切割

```shell
mv access.log access.log.0
kill -USR1 `cat /usr/local/nginx/logs/nginx.pid`
sleep 1
```

### Nginx安装第三方模块

下载第三方模块并解压，重新编译Nginx，执行命令 `./configure --prefix=/usr/local/nginx ... --add-module=../ngx_module_upload`, 并执行`make`, 不要使用`make install`

查看是否编译成功，是否添加了模块，`nginx -V`

备份nginx, `cp /usr/local/nginx/sbin/nginx{,.bak}`，停止并替换编译好的nginx。

### Nginx官方wiki

[nginx wiki](https://www.nginx.com/resources/wiki/start/)