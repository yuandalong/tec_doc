# redis-cli
* 指定库: -n

# 批量删除
redis-cli keys '123*' |xargs redis-cli del

# 获取有序集合
zrange key 0 -1

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
通配符：
* ？是单个字符的通配符
* *是任意个数的通配符
* [ae]会匹配到a或e
* ^e表示不匹配e
* a-c表示匹配a或b或c
* 特殊符号使用\隔开。

