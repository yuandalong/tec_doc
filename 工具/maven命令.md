clean 清理
compile 编译
install 发布到本地仓库
package 打包
deploy 发布到代理仓库
versions:set -DnewVersion=1.0.1-SNAPSHOT 修改版本号，主要用于多模块的项目，在父项目里执行
-Dmaven.test.skip=true 忽略junit
-X debug模式
-U 强制更新snapshot
-P 使用pom配置中指定的profile，如-P online 打包时就会使用profile 的id为online的配置
dependency:tree 打印依赖树