# 配置
修改 sqoop-env.sh

```shell
export HADOOP_COMMON_HOME=/home/hadoop/apps/hadoop-2.7.5

#Set path to where hadoop-*-core.jar is available
export HADOOP_MAPRED_HOME=/home/hadoop/apps/hadoop-2.7.5

#set the path to where bin/hbase is available
export HBASE_HOME=/home/hadoop/apps/hbase-1.2.6

#Set the path to where bin/hive is available
export HIVE_HOME=/home/hadoop/apps/apache-hive-2.3.3-bin

#Set the path for where zookeper config dir is
#zk在集群环境里貌似可以不配置
export ZOOCFGDIR=/home/hadoop/apps/zookeeper-3.4.10/conf
```

为什么在sqoop-env.sh 文件中会要求分别进行 common和mapreduce的配置呢？？？

> 在apache的hadoop的安装中；四大组件都是安装在同一个hadoop_home中的
> 但是在CDH, HDP中， 这些组件都是可选的。
> 在安装hadoop的时候，可以选择性的只安装HDFS或者YARN，
> CDH,HDP在安装hadoop的时候，会把HDFS和MapReduce有可能分别安装在不同的地方。

还需要将各种驱动包加到lib目录下，如mysql和hive

配置环境变量

```shell
export SQOOP_HOME=/home/hadoop/apps/sqoop-1.4.6
export PATH=$PATH:$SQOOP_HOME/bin
```

验证配置是否成功
`sqoop -version`

# 基本命令
通过`sqoop help`可以查看命令列表，主要就是export和import俩个命令

## 列出mysql数据库

```shell
sqoop list-databases \
--connect jdbc:mysql://hadoop1:3306/ \
--username root \
--password root
```

## 列出某个mysql库中的所有表

```shell
 sqoop list-tables \
--connect jdbc:mysql://hadoop1:3306/mysql \
--username root \
--password root
```

## 按mysql的表结构创建一个空hive表

```shell
sqoop create-hive-table \
--connect jdbc:mysql://hadoop1:3306/mysql \
--username root \
--password root \
--table help_keyword \
--hive-table help_keyword
```

# 导入数据

## 导入hdfs
### 语法

```shell
sqoop import (generic-args) (import-args)
```

### 常用参数

```shell
--connect <jdbc-uri> jdbc 连接地址
--connection-manager <class-name> 连接管理者
--driver <class-name> 驱动类
--hadoop-mapred-home <dir> $HADOOP_MAPRED_HOME
--help help 信息
-P 从命令行输入密码
--password <password> 密码
--username <username> 账号
--verbose 打印流程信息
--connection-param-file <filename> 可选参数
```

### 普通导入

导入mysql库中的help_keyword的数据到HDFS上
导入的默认路径：/user/hadoop/help_keyword

```shell
sqoop import   \
--connect jdbc:mysql://hadoop1:3306/mysql   \
--username root  \
--password root   \
--table help_keyword   \
-m 1
```

### 指定分隔符和导入路径

```shell
sqoop import   \
--connect jdbc:mysql://hadoop1:3306/mysql   \
--username root  \
--password root   \
--table help_keyword   \
--target-dir /user/hadoop11/my_help_keyword1  \
--fields-terminated-by '\t'  \
-m 2
```

### 带where条件

```shell
sqoop import   \
--connect jdbc:mysql://hadoop1:3306/mysql   \
--username root  \
--password root   \
--where "name='STRING' " \
--table help_keyword   \
--target-dir /sqoop/hadoop11/myoutport1  \
-m 1
```

### 查询指定列

```shell
sqoop import   \
--connect jdbc:mysql://hadoop1:3306/mysql   \
--username root  \
--password root   \
--columns "name" \
--where "name='STRING' " \
--table help_keyword  \
--target-dir /sqoop/hadoop11/myoutport22  \
-m 1
selct name from help_keyword where name = "string"
```
 
### 指定自定义查询SQL

```shell
sqoop import   \
--connect jdbc:mysql://hadoop1:3306/  \
--username root  \
--password root   \
--target-dir /user/hadoop/myimport33_1  \
--query 'select help_keyword_id,name from mysql.help_keyword where $CONDITIONS and name = "STRING"' \
--split-by  help_keyword_id \
--fields-terminated-by '\t'  \
-m 4
``` 

在以上需要按照自定义SQL语句导出数据到HDFS的情况下：
1. 引号问题，要么外层使用单引号，内层使用双引号，`$CONDITIONS的$`符号不用转义， 要么外层使用双引号，那么内层使用单引号，然后`$CONDITIONS的$`符号需要转义
2. 自定义的SQL语句中必须带有`WHERE \$CONDITIONS`

## 导入到Hive中

Sqoop 导入关系型数据到 hive 的过程是先导入到 hdfs，然后再 load 进入 hive

### 普通导入
数据存储在默认的default hive库中，表名就是对应的mysql的表名

```shell
sqoop import   \
--connect jdbc:mysql://hadoop1:3306/mysql   \
--username root  \
--password root   \
--table help_keyword   \
--hive-import \
-m 1
```

导入过程

* 第一步：导入mysql.help_keyword的数据到hdfs的默认路径
* 第二步：自动仿造mysql.help_keyword去创建一张hive表, 创建在默认的default库中
* 第三步：把临时目录中的数据导入到hive表中


查看数据

`hadoop fs -cat /user/hive/warehouse/help_keyword/part-m-00000`


### 指定行分隔符和列分隔符

指定hive-import，指定覆盖导入，指定自动创建hive表，指定表名，指定删除中间结果数据目录

```shell
sqoop import  \
--connect jdbc:mysql://hadoop1:3306/mysql  \
--username root  \
--password root  \
--table help_keyword  \
--fields-terminated-by "\t"  \
--lines-terminated-by "\n"  \
--hive-import  \
--hive-overwrite  \
--create-hive-table  \
--delete-target-dir \
--hive-database  mydb_test \
--hive-table new_help_keyword
```

**注意**hive-import，sqoop会自动给创建hive的表。 但是不会自动创建不存在的库

上面命令的hive-database和hive-table可以合并成下面的这种库名.表名的格式

```shell
sqoop import  \
--connect jdbc:mysql://hadoop1:3306/mysql  \
--username root  \
--password root  \
--table help_keyword  \
--fields-terminated-by "\t"  \
--lines-terminated-by "\n"  \
--hive-import  \
--hive-overwrite  \
--create-hive-table  \ 
--hive-table  mydb_test.new_help_keyword  \
--delete-target-dir
```

### 增量导入

执行增量导入之前，先清空hive数据库中的help_keyword表中的数据

`truncate table help_keyword;`

```shell
sqoop import   \
--connect jdbc:mysql://hadoop1:3306/mysql   \
--username root  \
--password root   \
--table help_keyword  \
--target-dir /user/hadoop/myimport_add  \
--incremental  append  \
--check-column  help_keyword_id \
--last-value 500  \
-m 1
```

## 导入到hbase

### 普通导入

```shell
sqoop import \
--connect jdbc:mysql://hadoop1:3306/mysql \
--username root \
--password root \
--table help_keyword \
--hbase-table new_help_keyword \
--column-family person \
--hbase-row-key help_keyword_id
```

**注意**需要先创建Hbase里面的表，再执行导入的语句

`create 'new_help_keyword', 'base_info'`

 
# 导出数据

## 命令参数

###常用参数

    --connect <jdbc-uri>：指定JDBC连接的数据库地址。
    --connection-manager <class-name>：指定要使用的连接管理器类。
    --driver <class-name>：手动指定要使用的JDBC驱动类。
    --hadoop-mapred-home <dir>：指定$ HADOOP_MAPRED_HOME路径
    --help：打印使用说明
    --password-file：为包含认证密码的文件设置路径。
    -P：从控制台读取密码。
    --password <password>：设置验证密码。
    --username <username>：设置验证用户名。
    --verbose：在工作时打印更多信息。
    --connection-param-file <filename>：提供连接参数的可选属性文件。
    --relaxed-isolation：将连接事务隔离设置为未提交给映射器的读取。

###验证参数

    --validate：启用对复制数据的验证，仅支持单个表复制。
    --validator <class-name>：指定要使用的验证程序类。
    --validation-threshold <class-name>：指定要使用的验证阈值类。
    --validation-failurehandler <class-name>：指定要使用的验证失败处理程序类。

###导出控制参数

    --columns <col,col,col…>：要导出到表格的列。
    --direct：使用直接导出快速路径。
    --export-dir <dir>：用于导出的HDFS源路径。
    -m,--num-mappers <n>：使用n个mapper任务并行导出。
    --table <table-name>：要填充的表。
    --call <stored-proc-name>：存储过程调用。
    --update-key <col-name>：锚点列用于更新。如果有多个列，请使用以逗号分隔的列列表。
    --update-mode <mode>：指定在数据库中使用不匹配的键找到新行时如何执行更新。mode包含的updateonly默认值（默认）和allowinsert。
    --input-null-string <null-string>：字符串列被解释为空的字符串。
    --input-null-non-string <null-string>：要对非字符串列解释为空的字符串。
    --staging-table <staging-table-name>：数据在插入目标表之前将在其中展开的表格。
    --clear-staging-table：表示可以删除登台表中的任何数据。
    --batch：使用批处理模式执行基础语句。

## 全量导出

```shell
sqoop export \
--connect jdbc:mysql://127.0.0.1:3306/test \
--username root \
--password 123456 \
--table kylin_country \
--export-dir /user/hive/warehouse/kylin_country \
--num-mappers 1
``` 

注意mysql中kylin_country表要提前创建

## 增量导出

### updateonly
updateonly只会导出mysql中已存在的，与hive里有差异的数据，不会导出hive新增的数据
update-mode默认是updateonly
**docker里各种报错，不建议用，直接用allowinsert吧**
```shell
sqoop export \
--connect jdbc:mysql://127.0.0.1:3306/test \
--username root \
--password 123456 \
--table kylin_country \
#可指定多个列，中间用逗号分隔
--update-key country \
--update-mode updateonly \
--export-dir /user/hive/warehouse/kylin_country \
--num-mappers 1
```

### allowinsert
allowinsert会将hive里修改过的和新增的数据都插入mysql
**而且会把mysql里做过修改的数据也覆盖掉**

```shell
sqoop export \
--connect jdbc:mysql://127.0.0.1:3306/test \
--username root \
--password 123456 \
--table kylin_country \
--export-dir /user/hive/warehouse/kylin_country \
--num-mappers 1 \
--update-key country \
--update-mode allowinsert
```
