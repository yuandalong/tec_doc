### Hbase行键列族的概念，物理模型，表的设计原则？

行键：是hbase表自带的，每个行键对应一条数据。

列族：是创建表时指定的，为列的集合，每个列族作为一个文件单独存储，存储的数据都是字节数组，其中数据可以有很多，通过时间戳来区分。

物理模型：整个hbase表会拆分成多个region，每个region记录着行键的起始点保存在不同的节点上，查询时就是对各个节点的并行查询，当region很大时使用.META表存储各个region的起始点，-ROOT又可以存储.META的起始点。

Rowkey的设计原则：各个列族数据平衡，长度原则、相邻原则，创建表的时候设置表放入regionserver缓存中，避免自动增长和时间，使用字节数组代替string，最大长度64kb，最好16字节以内，按天分表，两个字节散列，四个字节存储时分毫秒。

列族的设计原则：尽可能少(按照列族进行存储，按照region进行读取，不必要的io操作)，经常和不经常使用的两类数据放入不同列族中，列族名字尽可能短。

---
### 请列出正常的hadoop集群中hadoop都分别需要启动 哪些进程，他们的作用分别都是什么，请尽量列的详细一些。

namenode：负责管理hdfs中文件块的元数据，响应客户端请求，管理datanode上文件block的均衡，维持副本数量

Secondname:主要负责做checkpoint操作；也可以做冷备，对一定范围内数据做快照性备份。

Datanode:存储数据块，负责客户端对数据块的io请求

Jobtracker :管理任务，并将任务分配给 tasktracker。

Tasktracker: 执行JobTracker分配的任务。

Resourcemanager、Nodemanager、Journalnode、Zookeeper、Zkfc

---
### HBase简单读写流程
#### 读
找到要读数据的region所在的RegionServer，然后按照以下顺序进行读取：先去BlockCache读取，若BlockCache没有，则到Memstore读取，若Memstore中没有，则到HFile中去读。
#### 写
找到要写数据的region所在的RegionServer，然后先将数据写到WAL(Write-Ahead Logging，预写日志系统)中，然后再将数据写到Memstore等待刷新，回复客户端写入完成。

---
### HBase的特点是什么

1. hbase是一个分布式的基于列式存储的数据库，基于hadoop的HDFS存储，zookeeper进行管理。

1. hbase适合存储半结构化或非结构化数据，对于数据结构字段不够确定或者杂乱无章很难按一个概念去抽取的数据。

1. hbase为null的记录不会被存储。

1. 基于的表包括rowkey，时间戳和列族。新写入数据时，时间戳更新，同时可以查询到以前的版本。

1. hbase是主从结构。Hmaster作为主节点，hregionserver作为从节点。

 

### 请描述如何解决Hbase中region太小和region太大带来的结果

Region过大会发生多次compaction，将数据读一遍并写一遍到hdfs上，占用io，region过小会造成多次split，region会下线，影响访问服务，调整hbase.heregion.max.filesize为256m。