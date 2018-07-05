# ID生成策略

## 数据库

1. auto_increment 递增

简单，扩展性差，可用性难以保证

2. 批量生成ID

服务单点

## redis incr

## UUID,ObjectID

简单，存储空间大，没有连续性

## snowflake算法

19位id比较大，没有绝对递增，依赖服务器时间，如果服务器时间发生回拨，可能导致生成重复ID

## 第三方开源实现

### Leaf(美团)