# 面试题

## hive中存放的是什么？

表。

存的是和hdfs的映射关系，hive是逻辑上的数据仓库，实际操作的都是hdfs上的文件，HQL就是用SQL语法来写的MR程序。 

---
## Hive与关系型数据库的关系？

没有关系，hive是数据仓库，不能和数据库一样进行实时的CRUD操作。

是一次写入多次读取的操作，可以看成是ETL的工具。

---
## 请说明hive中Sort By、Order By、Cluster By，Distribute By各代表什么意思？

order by：会对输入做全局排序，因此只有一个reducer（多个reducer无法保证全局有序）。只有一个reducer，会导致当输入规模较大时，需要较长的计算时间。

sort by：不是全局排序，其在数据进入reducer前完成排序。

distribute by：按照指定的字段对数据进行划分输出到不同的reduce中。

cluster by：除了具有 distribute by 的功能外还兼具 sort by 的功能。

# hive操作
## 服务配置
### 单机配置
1. 配置hadoop
2. 配置mysql
3. 配置hive
`cp hive-default.xml.template hive-site.xml`
    1. 头部增加配置
  
        ```xml
        <property>
           <name>system:java.io.tmpdir</name>
           <value>/home/root/hdp/tmpdir</value>
        </property>
         
        <property>
             <name>system:user.name</name>
             <value>hive</value>
        </property>
        ```
    2. mysql配置
    修改以下节点值，配置文件模板里已经配置了，但用的是derby，此处改为mysql
    
    ```xml
            <!-- 插入一下代码 -->
        <property>
            <name>javax.jdo.option.ConnectionUserName</name>
            <value>root</value>
        </property>
        <property>
            <name>javax.jdo.option.ConnectionPassword</name>
            <value>123456</value>
        </property>
       <property>
            <name>javax.jdo.option.ConnectionURL</name>mysql
            <value>jdbc:mysql://192.168.1.68:3306/hive</value>
        </property>
        <property>
            <name>javax.jdo.option.ConnectionDriverName</name>
            <value>com.mysql.jdbc.Driver</value>
        </property>
            <!-- 到此结束代码 -->
    ```
1. 复制mysql的驱动程序到hive/lib下面
2. mysql建hive库
3. 初始化schema
    
    ```
    bin/schematool -dbType mysql -initSchema
    ```
4. 执行hive命令查看结果

### 启动服务
#### 其他机器的hive连
其他机器的hive连本机读取元数据，需要启动metastore
```shell
hive --service metastore
```
如上启动，会启动端口号默认9083的metastore服务，也可以通过-p指定端口号
其他服务器通过配置hive-site.xml来连接此服务器
```xml
    <property>
       <name>hive.metastore.uris</name>
       <!-- 此处是元数据服务器的ip或者host配置 -->
       <value>thrift://metastore_server_ip:9083</value>
    </property>
```

#### 其他类型的服务连
其他类型的服务，如java连hive，需要启动hiveserver2

1. hive-site.xml如1中配置
    hive-site.xml中可以加上是否需要验证的配置，此处设为NONE，暂时不需要验证，测试用。
    
    ```xml
    <property>
        <name>hive.server2.authentication</name>
        <value>NONE</value>
    </property>
    ```

1. hadoop的core-site.xml文件中配置hadoop代理用户

    ```xml
    <property>
        <name>hadoop.proxyuser.root.hosts</name>
        <value>*</value>
    </property>
    <property>
        <name>hadoop.proxyuser.root.groups</name>
        <value>*</value>
    </property>
    ```

1. 启动hiveserver2
    `hiveserver2`
    
1. 使用 beeline 测试
    ```shell
    beeline -u jdbc:hive2://localhost:10000
    ```
    hiveserver端口号默认是10000 
    使用beeline通过jdbc连接上之后就可以像client一样操作。
    
    hiveserver2会同时启动一个webui，端口号默认为10002，可以通过http://localhost:10002/访问
    界面中可以看到Session/Query/Software等信息。(此网页只可查看，不可以操作hive数据仓库)

#### 启动hiveWebInterface，通过网页访问hive

hive提供网页GUI来访问Hive数据仓库 
可以通过以下命令启动hwi，默认端口号9999

`hive --service hwi`

**从Hive 2.2.0开始不再支持hwi**

#### 使用HCatalog访问hive

从Hive版本0.11.0开始，hive包含了HCatalog 
HCatalog是基于Apache Hadoop之上的数据表和存储管理服务，支持跨数据处理工具，如Pig，Mapreduce，Streaming，Hive。 
使用HCatalog，则hive的元数据也可以为其他基于Hadoop的工具所使用。无论用户用哪个数据处理工具，通过HCatalog，都可以操作同一个数据。

可以通过以下命令启动HCatalog

`$HIVE_HOME/hcatalog/sbin/hcat_server.sh start`

可以通过以下命令启动HCatalog的cli界面

`$HIVE_HOME/hcatalog/bin/hcat`

另外，HCatalog的WebHCat 也提供一套REST API接口访问hive数据 
可以通过以下命令启动WebHCat

`$HIVE_HOME/hcatalog/sbin/webhcat_server.sh start`

[API接口官网地址](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference)



## hql

### 建库
`create database test`

### show

```
show tables;
show databases;
```

### 选择库

`use test`

### 建表

```sql
create table IF NOT EXISTS test (id int,name string)
-- 指定分区键
-- 注意分区键在hive分区表里是一个实际存在的字段，所以不能和其他字段名重复
-- 这个和mysql等关系型数据库不一样
PARTITIONED BY (date string)	
-- 指定序列化和反序列化的规则
ROW FORMAT DELIMITED
-- 列分隔符
FIELDS TERMINATED BY '\t'
-- 行分隔符
LINES TERMINATED BY '\n'
-- 文件存储格式
STORED AS TEXTFILE;
```

#### 查看建表语句
`show create table table_name`

### 插数据
#### insert

```sql
-- 追加数据 从Hive 1.1.0版本，TABLE关键字是可选的
INSERT INTO TABLE test VALUES(1,'ydl')
-- 覆盖数据 注意overwrite语句汇总table关键字不能省略
-- 注意并不是覆盖某一条数据，而是把表或者指定分区全覆盖了
INSERT OVERWRITE TABLE test VALUES(1,'ydl')
```
* INSERT OVERWRITE会覆盖表或分区中已存在的数据
* INSERT INTO以追加数据的方式插入到表或分区，原有数据不会删除
* Insert可以插入表或分区，如果表是分区表，则Insert时需要指定插入到哪个分区
* 从Hive 1.1.0版本，TABLE关键字是可选的
* 从Hive 1.2.0版本，INSERT INTO可以指定插入到哪些字段中，如INSERT INTO t(x,y,z)


#### load data
demo：
```sql
load  data local inpath '/datas/people.json' into table spark_people_json;
```

语法：
```sql
LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename 
[PARTITION (partcol1=val1,partcol2=val2 ...)]
```

- filepath 可以是： 
    相对路径，如project/data1
    绝对路径，如/user/hive/project/data1
    完整的URL，如hdfs://namenode:9000/user/hive/project/data1
- 目标可以是一个表或是一个分区。如果目标表是分区表，必须指定是要加载到哪个分区。
- filepath 可以是一个文件，也可以是一个目录(会将目录下的所有文件都加载)。
- 如果命令中带LOCAL，表示： 
    - load命令从本地文件系统中加载数据，可以是相对路径，也可以是绝对路径。对于本地文件系统，也可以使用完整的URL，如file:///user/hive/project/data1
    - load命令会根据指定的本地文件系统中的filepath复制文件到目标文件系统，然后再移到对应的表
- 如果命令中没有LOCAL，表示从HDFS加载文件，filepath可以使用完整的URL方式，或者使用fs.default.name定义的值
- 命令带OVERWRITE时加载数据之前会先清空目标表或分区中的内容，否则就是追加的方式。

#### 参考文档
[insert](https://blog.csdn.net/Post_Yuan/article/details/62887619)
[load data](https://blog.csdn.net/post_yuan/article/details/62883565)

### 清空表
#### 内部表

```sql
truncate table 表名; 
```

#### 外部表

外部表先清空元数据，然后删除hdfs上的文件

```sql
-- 删除20200931之前的元数据
alter table dw_zzpt_crawl_log_month drop partition(dt<='20200931')
```

```shell
#hdfs命令也可以使用通配符
#删除9月的hdfs数据文件
hdfs dfs -rm -r /home/user/hive/warehouse/dw.db/dw_zzpt_crawl_log_month/dt=202009*
```
由于外部表不能直接删除，所以用shell命令执行

```shell
#!/bin/bash
temp=$(date +%Y-%m-%d)
temp2=$(date -d "-1 day" +%Y-%m-%d)
hdfs dfs -rm -r /user/hive/test_table/partition_date=${temp}
```

### 删除表
`drop table table_name`


### 删除库
`drop database database_name`

### 删单条数据

没试验过，待验证
```sql
INSERT OVERWRITE TABLE "hive"."ods"."ods_news_etl_log_ymd" PARTITION(dt='20200514') 
SELECT * FROM "hive"."ods"."ods_news_etl_log_ymd" where dt = '20200514' and id != 'ZXB-ORIGIN-4338024'
```

### 修改表结构

```sql
-- 重命名表
ALTER TABLE name RENAME TO new_name
-- 添加字段
ALTER TABLE name ADD COLUMNS (col_spec[, col_spec ...])
-- 删除字段 貌似不支持
ALTER TABLE name DROP [COLUMN] column_name
-- 修改字段 可修改字段名和字段类型(z)，只能单个字段修改
ALTER TABLE name CHANGE column_name new_name new_type
-- 替换字段 可修改字段名和字段类型，注意此时是替换整个schema，
-- 而不是用第一个字段替换第二个，切记切记
ALTER TABLE name REPLACE COLUMNS (col_spec[, col_spec ...])
```

eg:

```sql
-- change改字段名
ALTER TABLE employee CHANGE name ename String;
-- change改字段类型
ALTER TABLE employee CHANGE salary salary Double;
-- replace
ALTER TABLE employee REPLACE COLUMNS ( 
eid INT empid Int, 
ename STRING name String);
-- 加字段
ALTER TABLE employee ADD COLUMNS ( 
dept STRING COMMENT 'Department name');
-- 重命名表
ALTER TABLE employee RENAME TO emp;
```

### 查看表结构
#### 查看字段类型
`desc table_name`

#### 查看完成表结构信息
`desc formatted table_name`

#### 查表的分区信息
查询某个表的分区信息：

`SHOW PARTITIONS employee;`

查看某个表是否存在某个特定分区键

`SHOW PARTITIONS employee PARTITION(country='US')`

`DESCRIBE EXTENDED employee PARTITION(country='US')`

#### 查看建表语句
`show create table table_name`

### 分区
Hive组织表到分区。它是将一个表到基于分区列，如日期，城市和部门的值相关方式。使用分区，很容易对数据进行部分查询。

	表或分区是细分成桶，以提供额外的结构，可以使用更高效的查询的数据。桶的工作是基于表的一些列的散列函数值。
	
语法：
```sql
ALTER TABLE table_name ADD [IF NOT EXISTS] PARTITION partition_spec
[LOCATION 'location1'] partition_spec [LOCATION 'location2'] ...;

partition_spec:
: (p_column = p_col_value, p_column = p_col_value, ...)
```

#### 添加分区

内部表不支持建表后添加和修改分区，需要建表的时候直接通过PARTITION BY语句指定


```sql
--外部表语法
ALTER TABLE employee
ADD PARTITION (year=’2012’)
location '/2012/part2012';
```

#### 修改分区

```sql
ALTER TABLE employee PARTITION (year=’1203’)
RENAME TO PARTITION (Yoj=’1203’);

```
#### 删除分区

```sql
ALTER TABLE employee DROP [IF EXISTS]
PARTITION (year=’2012’);
```

#### 插入数据到分区

```sql
--将t1表的数据加载到分区为day=2的表t2中  
INSERT OVERWRITE TABLE t2 PARTITION (day=2) SELECT * FROM t1;
--插入多个分区，数据源遍历多遍，效率低
INSERT OVERWRITE TABLE t2 PARTITION (day=2) SELECT * FROM t1;  
INSERT OVERWRITE TABLE t2 PARTITION (day=3) SELECT * FROM t1; 
--插入过个分区，数据源遍历一遍，效率高
FROM t1    
INSERT OVERWRITE TABLE t2 PARTITION(day=2) SELECT id WHERE day=2      
INSERT OVERWRITE TABLE t2 PARTITION(day=3) SELECT id WHERE day=3   
INSERT OVERWRITE TABLE t4 SELECT id WHERE day=4
--动态插入分区
--Hive是支持动态分区插入的。如果不支持的话，可以设置
--hive.exec.dynamic.partition=true;打开
set  hive.exec.dynamic.partition=true;
set  hive.exec.dynamic.partition.mode=nonstrict;
set  hive.exec.max.dynamic.partitions.pernode=1000;
--动态分区字段一定要放在所有静态字段的后面，这里业务字段在前，最后 a.province, 
--a.city作为动态分区字段会被赋到PARTITION (province, city)中  
INSERT OVERWRITE TABLE t2 PARTITION (province, city) SELECT ....... , a.province, a.city FROM a;
--插入单条指定数据的话select部分改成 select 1,2 from a limit 1这种,注意limit别漏了，要不然a表里有多少条就插入多少条了，而且a表必须是有数据的表，不能是空表，空表的话select语句不会返回数据，所以就不会插入数据到表里了


```

### 视图
#### 创建视图
语法：
```sql
CREATE VIEW [IF NOT EXISTS] view_name [(column_name [COMMENT column_comment], ...) ]
[COMMENT table_comment]
AS SELECT ...
```

eg:
```sql
CREATE VIEW emp_30000 AS
SELECT * FROM employee
WHERE salary>30000;
```

#### 删除视图
`DROP VIEW view_name`

### 临时表

Hive 0.14.0及以上支持
表只对当前session有效，session退出后，表自动删除。

语法：

`CREATE TEMPORARY TABLE ...`
注意点：
1. 如果创建的临时表表名已存在，那么当前session引用到该表名时实际用的是临时表，只有drop或rename临时表名才能使用原始表
2. 临时表限制：不支持分区字段和创建索引

从Hive1.1开始临时表可以存储在内存或SSD，使用hive.exec.temporary.table.storage参数进行配置，该参数有三种取值：memory、ssd、default。

### 索引
#### 创建索引
语法：
```sql
CREATE INDEX index_name
ON TABLE base_table_name (col_name, ...)
AS 'index.handler.class.name'
[WITH DEFERRED REBUILD]
[IDXPROPERTIES (property_name=property_value, ...)]
[IN TABLE index_table_name]
[PARTITIONED BY (col_name, ...)]
[
   [ ROW FORMAT ...] STORED AS ...
   | STORED BY ...
]
[LOCATION hdfs_path]
[TBLPROPERTIES (...)]
```

eg:
```sql
-- 创建一个名为index_salary的索引，对employee 表的salary列索引
CREATE INDEX inedx_salary ON TABLE employee(salary)
AS 'org.apache.hadoop.hive.ql.index.compact.CompactIndexHandler';
```

#### 删除索引
```sql
DROP INDEX index_salary ON employee
```

#### case when
语法:
`case when sale_type = '4' then '精选' else null end as sale_type`

### top N 查询
使用row_number结合partition by函数
eg:

```sql
select a.* from (select id,num, row_number() over(partition by id order 
by num desc) number from test_json) a where a.number <=2;
```
- 语法：row_number() over (partion by fieldA order by fieldB desc) rank
- 含义：表示根据fieldA分组，在分组内部根据fieldB排序，而row_number() 函数计算的值就表示每组内部排序后的行编号（该编号在组内是连续并且唯一的）。
- rank 在这里是别名，可任意
- partition by：类似于Hive的建表，分区的意思。
- order by ： 排序，默认是升序，加desc降序。

### 多行转一行
1. 数据文件及内容
    student.txt
    aoming|english|92.0
    xia|chinese|98.0
    xia|math|89.5
    huahua|chinese|80.0
    huahua|math|89.5

1. 创建表studnet：

    ```sql
    create table student(name string,subject string,score decimal(4,1))
    row format delimited
    fields terminated by '|';
    ```

1. 导入数据：

    ```sql
    load data local inpath '/home/hadoop/hivetestdata/student.txt' into table student;
    ```

1. 列转为行演示：

    ```sql
    select name,concat_ws(',',collect_set(subject)) from student group by name;
    ```
   ![](media/15713054528025.jpg)
    
    ```sql
    select name,concat_ws(',',collect_set(concat(subject,'=',score))) 
    from student group by name;
    ```
    ![](media/15713054907101.jpg)


#### concat
拼接字符串
#### concat_ws
concat的特殊形式
CONCAT_WS() 代表 CONCAT With Separator
语法：
CONCAT_WS(separator,str1,str2,…)
第一个参数是其它参数的分隔符
可以对array类型的字段进行拼接，此时会把array的各个元素按分隔符拼接成一个字符串
#### collect_set
多行合并成一行，并去重
#### collect_list
多行合并成一行，不去重

### 一行转多行
```sql
explode(split(a.usertags, ',')) 
```
#### explode
集合转多行
explode(ARRAY) 列表中的每个元素生成一行
explode(MAP) map中每个key-value对，生成一行，key为一列，value为一列

#### split
使用特殊字符将指定字段切分成集合

#### lateral view

lateral view(侧视图)的意义是配合explode（或者其他的UDTF）使用的
explode函数有几个限制：
- No other expressions are allowed in SELECT
    `SELECT pageid, explode(adid_list) AS myCol... is not supported`
- UDTF's can't be nested  
    `SELECT explode(explode(adid_list)) AS myCol... is not supported`
- GROUP BY / CLUSTER BY / DISTRIBUTE BY / SORT BY is not supported
    `SELECT explode(adid_list) AS myCol ... GROUP BY myCol is not supported`
    
此时使用lateral view可以突破这些限制

用法：
```sql
select goods_id2,sale_info from explode_lateral_view LATERAL VIEW 
explode(split(goods_id,','))goods as goods_id2;
``` 
其中LATERAL VIEW explode(split(goods_id,','))goods相当于一个虚拟表，与原表explode_lateral_view笛卡尔积关联。

也可以多重使用
```sql
select goods_id2,sale_info,area2
from explode_lateral_view 
LATERAL VIEW explode(split(goods_id,','))goods as goods_id2 
LATERAL VIEW explode(split(area,','))area as area2;
```
也是三个表笛卡尔积的结果


 

    
## 特殊关系运算符
|运算符|操作|描述|
| --- | --- | --- |
|A RLIKE B|字符串|NULL，如果A或B为NULL；TRUE，<br>如果A任何子字符串匹配Java正则表达式B；否则FALSE。|
|A REGEXP B|字符串|等同于RLIKE.|


## hive内置函数

|返回类型| 签名| 描述|
| --- | --- | --- |
| BIGINT| round(double a)| 返回BIGINT最近的double值。|
| BIGINT| floor(double a)| 返回最大BIGINT值等于或小于double。|
| BIGINT| ceil(double a)| 它返回最小BIGINT值等于或大于double。|
| double| rand(), rand(int seed)| 它返回一个随机数，从行改变到行。|
| string| concat(string A, string B,...)| 它返回从A后串联B产生的字符串|
| string| substr(string A, int start)| 它返回一个起始，从起始位置的子字符串，直到A.结束|
| string| substr(string A, int start, int length)| 返回从给定长度的起始start位置开始的字符串。|
| string| upper(string A)| 它返回从转换的所有字符为大写产生的字符串。|
| string| ucase(string A)| 和上面的一样|
| string| lower(string A)| 它返回转换B的所有字符为小写产生的字符串。|
| string| lcase(string A)| 和上面的一样|
| string| trim(string A)| 它返回字符串从A.两端修剪空格的结果|
| string| ltrim(string A)| 它返回A从一开始修整空格产生的字符串(左手侧)|
| string| rtrim(string A)| rtrim(string A)，它返回A从结束修整空格产生的<br>字符串(右侧)|
| string| regexp_replace(string A, string B, string C)| 返回从替换所<br>有子在B结果配合C.在Java正则表达式语法的字符串|
| int| size(Map<K.V>)| 它返回在映射类型的元素的数量。|
| int| size(Array<T>)| 它返回在数组类型元素的数量。|
| value of <type>| cast(<expr> as <type>)| 它把表达式的结果expr<类型>如cast('1'作为BIGINT)<br>代表整体转换为字符串'1'。如果转换不成功，返回的是NULL。|
| string| from_unixtime(int unixtime)| 转换的秒数从Unix纪元(1970-01-0100:00:00 UTC)代表那一刻，<br>在当前系统时区的时间戳字符的串格式："1970-01-01 00:00:00"|
| string| to_date(string timestamp)| 返回一个字符串时间戳的日期部分：<br>to_date("1970-01-01 00:00:00") = "1970-01-01"|
| int| year(string date)| 返回年份部分的日期或时间戳字符串：<br>year("1970-01-01 00:00:00") = 1970, year("1970-01-01") = 1970|
| int| month(string date)| 返回日期或时间戳记字符串月份部分：<br>month("1970-11-01 00:00:00") = 11, month("1970-11-01") = 11|
| int| day(string date)| 返回日期或时间戳记字符串当天部分：<br>day("1970-11-01 00:00:00") = 1, day("1970-11-01") = 1|
| string| get_json_object(string json_string, string path)| 提取从基于指定的JSON路径的JSON字符串JSON对象，<br>并返回提取的JSON字符串的JSON对象。<br>如果输入的JSON字符串无效，返回NULL。|




# 加载json数据
## 整条数据是json格式
1. 添加jar包
    将**hive-hcatalog-core.jar**添加到hive的jar目录下，需要注意版本与hive对应，可从maven中央仓库下载
2. 建表并指定序列化格式
    
     ```sql
     create table test_json(id int,num int)
     ROW FORMAT SERDE 'org.apache.hive.hcatalog.data.JsonSerDe';
     ```
     
3. 导入数据测试
    
    ```sql
    load data local inpath '/root/data/json_data' into table test_json;
    ```
    
[参考文档](https://blog.csdn.net/lsr40/article/details/79399166)

### 解决嵌套的json数据
如果是json对象则字段类型使用STRUCT，如果是json数组，则字段类型使用ARRAY
[参考文档](https://blog.csdn.net/weixin_43215250/article/details/93783266)

## 个别字段是json格式
两个hive函数可以处理json字段：
- get_json_object()
    get_json_object函数第一个参数填写json对象变量，第二个参数使用$表示json变量标识，然后用 . 或 [] 读取对象或数组；
    eg:
    ```sql
    select get_json_object('{"shop":{"book":[{"price":43.3,"type":"art"},
    {"price":30,"type":"technology"}],"clothes":
    {"price":19.951,"type":"shirt"}},"name":"jane","age":"23"}', '$.shop.book[0].type') 
    
    -- 如果json简单，可以直接这样使用：
    select get_json_object('{"name":"jack","server":"www.qq.com"}','$.server') 
    ```
    但是问题来了get_json_object**每次只能查一个字段**
    例如：
    
    ```sql
    select get_json_object('{"name":"jack","server":"www.qq.com"}','$.server','$.name')
    ```
    此时只能多写几个get_json_object，比较麻烦，所以json_tuple方法就派上了用场。

- json_tuple()
    eg:
    ```sql
    select json_tuple('{"name":"jack","server":"www.qq.com"}','server','name')
    ```
    但是缺点就是对于复杂的嵌套的json，就操作不了了（就是说使用不了"."，“[]”这种符号来操作json对象），所以看情况选择这两个方法去使用。

# hive数据类型
Hive所有数据类型分为四种类型，给出如下：
## 列类型
### 整型
![](media/15709718232185.jpg)

### 字符串类型
![](media/15709718703240.jpg)

### 时间戳

它支持传统的UNIX时间戳可选纳秒的精度。它支持的java.sql.Timestamp格式“YYYY-MM-DD HH:MM:SS.fffffffff”和格式“YYYY-MM-DD HH:MM:ss.ffffffffff”。

### 日期

DATE值在年/月/日的格式形式描述 {{YYYY-MM-DD}}.

### 小数点

在Hive 小数类型与Java大十进制格式相同。它是用于表示不可改变任意精度。语法和示例如下：

```
DECIMAL(precision, scale)
decimal(10,0)
```

### 联合类型

联合是异类的数据类型的集合。可以使用联合创建的一个实例。语法和示例如下：
UNIONTYPE<int, double, array<string>, struct<a:int,b:string>>

```
{0:1} 
{1:2.0} 
{2:["three","four"]} 
{3:{"a":5,"b":"five"}} 
{2:["six","seven"]} 
{3:{"a":8,"b":"eight"}} 
{0:9} 
{1:10.0}
```

## 文字
### 浮点类型

浮点类型是只不过有小数点的数字。通常，这种类型的数据组成DOUBLE数据类型。

### 十进制类型

十进制数据类型是只不过浮点值范围比DOUBLE数据类型更大。十进制类型的范围大约是 `-10**-308 到 10**308`.

## Null 值
缺少值通过特殊值 - NULL表示。
## 复杂类型
### 数组

在Hive 数组与在Java中使用的方法相同。
`ARRAY<data_type>`

### 映射

映射在Hive类似于Java的映射。
`MAP<primitive_type, data_type>`

### 结构体

在Hive结构体类似于使用复杂的数据。
`STRUCT<col_name : data_type [COMMENT col_comment], ...>`

# java操作hive
https://www.cnblogs.com/takemybreathaway/articles/9750175.html

# spark写hive
https://blog.csdn.net/a2639491403/article/details/80044121
大体思路是先写入临时表，然后执行insert into table select * from tmp

# 随机抽样
在大规模数据量的数据分析及建模任务中，往往针对全量数据进行挖掘分析时会十分耗时和占用集群资源，因此一般情况下只需要抽取一小部分数据进行分析及建模操作。Hive提供了数据取样（SAMPLING）的功能，能够根据一定的规则进行数据抽样，目前支持数据块抽样，分桶抽样和随机抽样，具体如下所示：

## 数据块抽样（tablesample()函数） 
1. tablesample(n percent) 根据hive表数据的大小按比例抽取数据，并保存到新的hive表中。如：抽取原hive表中10%的数据 
    **注意** 测试过程中发现，select语句不能带where条件且不支持子查询，可通过新建中间表或使用随机抽样解决） 
`create table xxx_new as select * from xxx tablesample(10 percent) `
2. tablesample(n M) 指定抽样数据的大小，单位为M。 
3. tablesample(n rows) 指定抽样数据的行数，其中n代表每个map任务均取n行数据，map数量可通过hive表的简单查询语句确认（关键词：number of mappers: x)

## 分桶抽样 
hive中分桶其实就是根据某一个字段Hash取模，放入指定数据的桶中，比如将表table_1按照ID分成100个桶，其算法是hash(id) % 100，这样，hash(id) % 100 = 0的数据被放到第一个桶中，hash(id) % 100 = 1的记录被放到第二个桶中。创建分桶表的关键语句为：CLUSTER BY语句。 
分桶抽样语法： 
`TABLESAMPLE (BUCKET x OUT OF y [ON colname]) `
其中x是要抽样的桶编号，桶编号从1开始，colname表示抽样的列，y表示桶的数量。 
例如：将表随机分成10组，抽取其中的第一个桶的数据 
`select * from table_01 tablesample(bucket 1 out of 10 on rand()) where p_day=20190508 limit 10;
`
## 随机抽样（rand()函数） 
1）使用rand()函数进行随机抽样，limit关键字限制抽样返回的数据，其中rand函数前的distribute和sort关键字可以保证数据在mapper和reducer阶段是随机分布的，案例如下： 
`select * from table_name where col=xxx distribute by rand() sort by rand() limit num; `
2）使用order 关键词 
案例如下： 
`select * from table_name where col=xxx order by rand() limit num; `
经测试对比，千万级数据中进行随机抽样 order by方式**耗时更长**，大约多30秒左右。

# 常见错误

## Call to localhost/127.0.0.1:9000 failed on connection exception错误
1. 首先查看hdfs-site.xml配置文件，如下面所示

    ```xml
    <property>
        <name>dfs.namenode.secondary.http-address</name>
        <value>Master:50090</value>
    </property>
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file:/usr/local/hadoop/tmp/dfs/name</value>
    </property>
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file:/usr/local/hadoop/tmp/dfs/data</value>
    </property>
    <property>
        <name>dfs.replication</name>
        <value>2</value>
    </property>
    
    ```
1. 将目录/usr/local/hadoop/tmp/dfs/name和/usr/local/hadoop/tmp/dfs/data中的内容清空。
2. 然后执行bin/hadoop namenode -format命令
3. 重启hadoop
4. 问题解决

## 其他用户没有写数据权限
大多是由于hdfs是用root启动的，其他用户没有些文件的权限
通过hdfs命令来修改文件权限
`hadoop dfs -chmod -R 777 /user`

# 内部表和外部表的区别
[参考文档](https://blog.csdn.net/qq_36743482/article/details/78393678)
未被external修饰的是内部表（managed table），被external修饰的为外部表（external table）；
## 区别
- 内部表数据由Hive自身管理，外部表数据由HDFS管理；
- 内部表数据存储的位置是hive.metastore.warehouse.dir（默认：/user/hive/warehouse），外部表数据的存储位置由自己制定（如果没有LOCATION，Hive将在HDFS上的/user/hive/warehouse文件夹下以外部表的表名创建一个文件夹，并将属于这个表的数据存放在这里）；
- 删除内部表会直接删除元数据（metadata）及存储数据；删除外部表仅仅会删除元数据，HDFS上的文件并不会被删除；
- 对内部表的修改会将修改直接同步给元数据，而对外部表的表结构和分区进行修改，则需要修复（MSCK REPAIR TABLE table_name;）

# 时间戳格式化

```sql
-- presto
select format_datetime(from_unixtime( cast(1564581347793/1000 as int)),'yyyy-MM-dd HH:mm:ss')

-- hive
select from_unixtime( cast(1564581347793/1000 as int),'yyyy-MM-dd HH:mm:ss')
```