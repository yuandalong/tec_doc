# 基础命令
* clean 清理
* compile 编译
* install 发布到本地仓库
* package 打包
* deploy 发布到代理仓库

# 修改版本号
`versions:set -DnewVersion=1.0.1-SNAPSHOT` 
修改版本号，主要用于多模块的项目，在父项目里执行

# 忽略junit
`-Dmaven.test.skip=true`

# debug
-X debug模式

# 强制更新sanpshot
-U 强制更新snapshot

# 指定配置
-P 使用pom配置中指定的profile，如-P online 打包时就会使用profile 的id为online的配置

# 打印依赖树
`dependency:tree` 打印依赖树

# 查看当前生效的配置
## 查看当前生效的settings
`help:effective-settings`

## 查看当前生效的pom
`help:effective-pom`

## 查看当前处于激活状态的profile
`help:active-profiles`
