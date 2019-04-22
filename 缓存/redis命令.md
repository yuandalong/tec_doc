### redis-cli
* 指定库: -n

### 批量删除
redis-cli keys '123*' |xargs redis-cli del

### 获取有序集合
zrange key 0 -1