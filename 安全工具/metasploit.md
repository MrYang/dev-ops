# Metasploit

## 安装

先安装postgresql `sudo apt install postgresql postgresql-contrib`

`service postgresql status/start/stop` 检测pgsql状态

`sudo passwd postgres` 设置postgres 默认用户密码，才能连接pgsql

`sudo -u postgres psql` 切换postgres 用户并连接postgresql

`wget https://downloads.metasploit.com/data/releases/metasploit-latest-linux-x64-installer.run` 下载下来，然后sudo 执行脚本

