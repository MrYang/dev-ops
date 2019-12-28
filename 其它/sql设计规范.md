## Sql设计规范

### 基本规范

- 数据库，表字符集全部设置成utf-8
- 统一使用INNODB存储引擎
- 数据库，表需要添加注释，含义比较模糊或者跟业务紧密相关的字段添加注释
- 数据库不存图片等二进制文件
- 使用英文单词小写，不使用拼音，多个单词之间使用下划线连接

### 命名规范

- 不加info后缀, 如用user就能很明确表明是用户实体, 没必要加info后缀

### 表规范

- 数据表必须有主键，使用auto_increment的id作为主键（与业务无关），类型为bigint,业务相关的id做为唯一索引
- 对相关功能的表应当使用相同前缀,如order_pic, order_detail,order_other
- 表字段控制在20个以内
- 每个表都添加create_date, update_date两个字段
- 表关联不使用外键,统一由程序处理

### 字段规范

- 外键id使用table_id 命名，并尽量定义在表的前面
- 所有字段如有必要尽量设置为非空
- 时间字段统一使用datetime类型
- 不使用mysql关键字作为字段名,如desc, status
- 不使用TEXT、BLOB类型
- 禁止在数据库中存储明文密码

### 索引规范

- 索引名以idx_filed 命名, 如idx_username, 唯一索引以uidx为前缀
- 索引不要多余5个，联合索引中的字段不要多余5个
- 避免冗余或重复索引，如index(a，b，c)相当于index(a)、index(a，b)、index(a，b，c)；
- 不在索引列进行数学运算和函数运算；
- 不使用%前导的查询，如like“%xxx”，无法使用索引
- 不使用反向查询，如not in / not like，无法使用索引，导致全表扫描

### 一条好用的sql

appinfo表中有一个字段名字叫做owner_id，这个字段里面存的值是用逗号隔开的多个人的工号，那么我想随便拿一个工号，按照owner_id这个字段去进行查询，就可以使用以下sql语句
`select * from appinfo where concat (',',owner_id,',') regexp ',123123,'`

`select month(time), count(case when a>2 then a-1 else 1 end )  from log where and a > 0 group by month(time);`
