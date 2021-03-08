# docker环境

```shell
#拉镜像
docker pull apachekylin/apache-kylin-standalone:3.1.0
#启动
docker run -d \
-m 8G \
-p 7070:7070 \
-p 8088:8088 \
-p 50070:50070 \
-p 8032:8032 \
-p 8042:8042 \
-p 16010:16010 \
apachekylin/apache-kylin-standalone:3.1.0
#进入
docker exec -it docker_id bash
```

在容器启动时，会自动启动以下服务：
* NameNode, DataNode
* ResourceManager, NodeManager
* HBase
* Kafka
* Kylin

容器启动后，我们可以通过 `docker exec -it <container_id> bash` 命令进入容器内。当然，由于我们已经将容器内指定端口映射到本机端口，我们可以直接在本机浏览器中打开各个服务的页面，如：

* Kylin 页面：http://127.0.0.1:7070/kylin/login 默认用户名密码ADMIN/KYLIN
* HDFS NameNode 页面：http://127.0.0.1:50070
* YARN ResourceManager 页面：http://127.0.0.1:8088
* HBase 页面：http://127.0.0.1:16010

## 相关命令
### kylin
$KYLIN_HOME/bin

```shell
#启动
kylin.sh start
#停止
kylin.sh stop
```

### hbase
$HBASE_HOME/bin

```shell
#启动
start-hbase.sh
#停止
stop-hbase.sh
```

**docker里zk用的hbase自带的zk，zk出问题的话直接重启hbase就行**

# 概念

## CUBE

* Table: 作为cubes源的hive表;在构建cubes前要先sync
* Data Model: 描述星型模式数据模型;定义fact/lookup表和过滤条件
* Cube Descriptor: cube实例的定义和设置;定义使用的model,要包含的dimensions和measures,如何分区segments和处理自动合并等;
* Cube Instance: cube实例;从Cube Descriptor构建,包含一个或多个cube segments;
* Partition: 可以在Cube Descriptor定义DATE/STRING类型的字段作为分区字段,将一个cube分区为几个带有日期区间的segments
* Cube Segment: cube数据的实际载体,对应为HBase的HTable;**cube实例的一次构建任务创建一个新的segment;如果某个日期区间的数据改变了,可以刷新相应的segments,而不用重新构建整个cube;**
* Aggregation Group: 一个聚合组是dimensions的子集,在此内部组合构建cuboid;此项是为了优化时裁剪;

## DIMENSION & MEASURE

* Mandotary: 这个维度类型用于cuboid裁剪,如果一个维度定义为"必要的(mandatory)",没有包信这个维度的维度组合会被裁剪;
* Hierarchy: 这个维度类型用于cuboid裁剪,如果维度A,B,C有"层级(hierarchy)"关系,维度组合只保留A,AB或ABC
* Derived: 在lookup表集中,一些维度集可能是从它的PK(主键)产生的,所以在它们和fact表的FK(外键)有特定的对应关系;而这些维度就是"衍生的(DERIVED)",可以不用参于cuboid的生成;
* Count Distinct(HyperLogLog): 立即的COUNT DISTINCT很难计算,引入近似算法HyperLogLog,并将错误率保存在较低水平;
* Count Distinct(Precise): 精确的COUNT DISTINCT预计算,是基于RoaringBitmap的,当前只支持INT或BIGINT;
* Top N: 预计算top N

## CUBE ACTIONS

* BUILD: 给定分区字段的区间,这个动作会构建新的cube segment
* REFRESH: 这个动作会根据分区区间重新构建cube segment,用于源表数据的增长;
* MERGE: 将多个连续的cube segments合并;可以在cube descriptor里定义自动合并设置;
* PURGE: 清空cube实例的segments;**这只会更新元信息,不会删除HBase上的cube数据;**

## JOB STATUS

* NEW: 一个任务被创建了
* PENDING: 任务计划暂停了一个任务,等待资源;
* RUNNING: 任务在运行
* FINISHED: 任务完成
* ERROR: 任务因错误退出
* DISCARDED: 用户中止了任务;

## JOB ACTION

* RESUME: 从最新的成功点开始,重试ERROR状态的任务
* DISCARD: 用户中止任务;



## Dimensions
维度，用来group by的

## Measures
值，用来聚合的，只能是事实表的字段