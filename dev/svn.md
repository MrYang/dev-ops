# SVN

### 安装

`yum install subversion` 搞定

### Server

- 创建版本库

`mkdir /var/svn/project && svnadmin create /var/svn/project`

- 设置用户密码及权限

进入`/var/svn/project/conf`目录，其中`authz`文件是权限控制文件，`passwd`是帐号密码文件，`svnserve.conf`是SVN服务配置文件

编辑`svnserve.conf`，打开以下几个注释
```shell
anon-access = read #匿名用户可读
auth-access = write #授权用户可写
password-db = passwd #使用哪个文件作为账号文件
authz-db = authz #使用哪个文件作为权限文件
realm = /var/svn/project # 认证空间名，版本库所在目录
```
编辑`passwd`文件，在`[users]`块中添加用户和密码，格式：`帐号=密码`，如`xuebing=123456`
```ini
[users]
xuebing=123456
```
编辑`authz`文件，在末尾添加
```shell
[/]
xuebing=rw
```

- 添加自启动

`echo svnserve -d -r /var/svn/project >> /etc/rc.local`

### Client

- 所有命令都可以加 `--help`参数查询帮助，如`svn checkout --help`
- 从svn服务器checkout， `svn checkout svn://192.168.1.1/project`, 简写`svn co`
- 添加文件， `svn add file`
- 提交，`svn commit -m 'commit message' path`，简写`svn ci`，如果不写path，默认提交全部改动
- 更新，`svn update -r 200 path`，简写`svn up`，如果不加-r, path参数，默认更新所有到最新版本
- 查看状态，`svn status path`， 简写`svn st`
- 删除文件，`svn delete path`，然后再`svn commit -m 'delete path'`
- 查看日志，`svn log path`，`svn log -l 10 path`，查看最近10条日志
- 文件比较，`svn diff -r m:n path`，比较版本m,n之间的差别，简写`svn di`
- 解决冲突，`svn resolved path`， 注意: 该命令不会依语法来解决冲突或是移除冲突标记；它只是移除冲突的相关文件，然后让 path 可以再次提交。
- [分支操作](http://git.oschina.net/yxb_1990/blogs/blob/master/svn%E5%88%86%E6%94%AF%E6%93%8D%E4%BD%9C.md)