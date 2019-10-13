# redis-cli
* 指定库: -n

# 批量删除
redis-cli keys '123*' |xargs redis-cli del

# 有序集合操作
## 根据索引查询
zrange key 0 -1

## 根据分值查询
zrangbyscore key 0 100

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

