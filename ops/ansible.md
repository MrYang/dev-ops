# ansible

### 安装配置

`pip install ansible` 即可

建立`/etc/ansible/hosts`文件，输入以下内容

```shell
192.168.89.10
[groupA]
192.168.89.11
[groupB]
192.168.89.12
```

执行命令`ansible groupA -m ping` 即可查看`192.168.89.11`的ping情况

### 即时命令

使用`-a`指定命令， `-f`指定并发数（默认为5）, `-m`是选择使用的模块，不指定`-m`参数时，默认使用`command`模块

```shell
ansible all -a "/bin/echo hello" # 打印hello
ansible all -a "/sbin/reboot" -f 10 --sudo -K # 重启

ansible groupA -m shell -a ". .bash_profile;ps -fe |grep sa_q" #执行远程命令

ansible all -m copy -a "src=~/projects/tests/t.py dest=~" # 传输文件
ansible all -m file -a "dest=~/t.py mode=777 owner=ashin group=ashin" # 修改文件权限，所有者，分组（这些参数可以用在copy模块中）
ansible all -m file -a "dest=~/tests mode=755 owner=ashin group=ashin state=directory" # 创建文件夹
ansible all -m file -a "dest=~/tests state=absent" # 删除文件夹

# 确保某个程序已经安装，并保持当前版本，如果没安装则进行安装
ansible groupA -m apt -a "name=python-pip state=present" --sudo -K
# 确保安装最新版本
ansible groupA -m apt -a "name=git state=latest"
# 确保没有安装某个程序，安装了则卸载
ansible groupA -m apt -a "name=git state=absent" --sudo -K

ansible groupA -m service -a "name=mysql state=started" --sudo -K
ansible groupA -m service -a "name=mysql state=restarted" --sudo -K
ansible groupA -m service -a "name=mysql state=stopped" --sudo -K

ansible groupA -m script -a "/opt/app/target.sh" #远程服务器上执行本地脚本
```

### Playbooks