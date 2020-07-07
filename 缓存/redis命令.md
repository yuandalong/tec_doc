# redis-cli
* 指定库: -n

# 批量删除
redis-cli keys '123*' |xargs redis-cli del

# 有序集合操作（sorted_set）
[参考文档](http://www.redis.cn/commands.html#sorted_set)
## 根据索引查询
zrange key 0 -1

## 根据分值查询
zrangebyscore key 0 100

## 查key的索引，可用于判断是否存在
zrank key member

## 添加
不存在时add 存在时update
zadd key 100 test
100是score，test是member

## 查member的score
zscore key member

## 查key的成员数
zcard key

# 切换当前使用的库
select 1

# keys
```shell
#查所有，生产不建议用
keys *
#查a开头
keys a*
#查a结尾
keys *a


```
## 通配符
* ？是单个字符的通配符
* *是任意个数的通配符
* [ae]会匹配到a或e
* ^e表示不匹配e
* a-c表示匹配a或b或c
* 特殊符号使用\隔开。

# hash操作
[参考文档](http://www.redis.cn/commands.html#hash)
## 获取所有field和value

`hgetall key`

## 获取所有field
`hkeys key`

## 同时将多个 field-value (域-值)对设置到哈希表 key 中
`hmset key FIELD1 VALUE1 FIELD2 VALUE2`

## 同时获取多个字段的值
`hmget key FIELD1 FIELD2`

## 设置单个字段的值
`hset key FIELD1 VALUE1`

## 获取单个字段的值
`hget key FIELD1`

# set操作
[参考文档](http://www.redis.cn/commands.html#set)
## 所有成员
`SMEMBERS key`

## 成员个数
`SCARD key`

## 添加成员
`SADD key member`

## 是否包含
`SISMEMBER key member`


# list操作
## 所有元素
`LRANGE mylist 0 -1`
-1表示最后一个元素，一次类推，-2就是最后第二个

##  入队
右侧入队
`RPUSH mylist "hello"`

左侧入队
`LPUSH mylist "hello"`

## 修改值
`LSET mylist 0 "four"`

## 出队
左侧出队
`LPOP mylist`

右侧出队
`RPOP mylist`

## 修剪
`LTRIM mylist 1 -1`
保留第二个到最后一个元素，相当于删除第一个元素

## 阻塞式出队
左出队
`BLPOP list1 list2 0`

右出队
`BRPOP list1 list2 0`

0为超时时间，表示不超时，可根据情况设置超时时间

可同时监控多个队列，当队列里有值时立刻返回，返回的是队列名和元素值，当队列里没值时会阻塞直至有元素入队并返回

## 长度
`LLEN list1`