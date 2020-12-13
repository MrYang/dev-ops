# nmap

## 安装

`apt-get install nmap`

## 参数

- `nmap 192.168.1.1/24` 随机选择1000个端口进行扫描
- `nmap -p 3306, 100-200 ip` 扫描端口
- `nmap -F ip` 扫描100个常用端口
- `nmap -sV ip` 检测服务端软件的版本信息
- `nmap -O ip` 检测操作系统
- `nmap -Pn ip` 禁用主机检测

## TCP 扫描参数

- `-sT` 三次握手扫描,非root权限使用
- `-sS` SYN标志位扫描，非常快，root权限时默认使用
- `-sA` 检测目标系统是否采用了数据包状态监测技术,防火墙
- `-sU` UDP 扫描

## script

- `--script dns-brute` 使用脚本扫描，dns-brute 为子域名破解脚本, 脚本目录 `https://nmap.org/nsedoc/index.html` 

## 其它

- `-f` 使用小数据包
- `--source-port` 模拟端口
- `--max-parallelism` 并发扫描的最大连接数

## ncat 命令

- `ncat -zv http:url 80`
- `ncat -l 9000` 监听9000端口
- `ncat -v 127.0.0.1 9000` 连接服务器
- `ncat -v -lp 9000 < file.txt` 发送文件到客户端, `ncat -v 127.0.0.1 9000 > file.txt` 从服务端接收文件
- `ncat --exec "/bin/bash" -l 9000 --keep-open` 在本机的 9000 端口分享 Bash Shell，让对方可以通过网络访问此端口来自由访问
- `ncat -l --proxy-type http localhost 8888`  在本机 8888 端口创建 HTTP 代理