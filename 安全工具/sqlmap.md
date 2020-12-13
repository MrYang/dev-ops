# sqlmap

## 安装

`apt-get install sqlmap`


## 参数使用

- `-v 3` 输出等级，默认为1
- `--data "password=xxx"` post参数
- `--cookie "admin=1"` 附带cookie
- `-level=1` 默认级别为1

## 使用示例

- `sqlmap -u "httpurl?id=1" -os Linux –dbms mysql –level 3` 自动检测，指定数据库类型为mysql 级别为3，默认使用级别1
- `sqlmap -u "httpurl_*"` url重写检测
- `sqlmap -m target.txt` 批量检测
- `sqlmap -d dburl --dbs --users` 直接连接数据库,列出数据库信息，用户信息
- `sqlmap -u httpurl_* --current-db --current-user` 当前使用的数据库及当前用户
- `sqlmap -u httpurl_* -D database --tables` 指定数据库所有表
- `sqlmap -u httpurl_* -D database -T table --columns` 指定库，表的字段
- `sqlmap -u httpurl?id=1 -D database -T table -C"column1,column2,column3" --start 1 --stop 10 --dump` dump 前10行数据
