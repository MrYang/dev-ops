# ansible

### 安装配置

`pip install ansible` 即可

建立`/etc/ansible/hosts`文件，输入一下内容

```shell
192.168.89.10
[groupA]
192.168.89.11
[groupB]
192.168.89.12
```

执行命令`ansible groupA -m ping` 即可查看`192.168.89.11`的ping情况