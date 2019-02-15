### hive中存放的是什么？

表。

存的是和hdfs的映射关系，hive是逻辑上的数据仓库，实际操作的都是hdfs上的文件，HQL就是用SQL语法来写的MR程序。 

---
### Hive与关系型数据库的关系？

没有关系，hive是数据仓库，不能和数据库一样进行实时的CRUD操作。

是一次写入多次读取的操作，可以看成是ETL的工具。

---
### 请说明hive中Sort By、Order By、Cluster By，Distribute By各代表什么意思？

order by：会对输入做全局排序，因此只有一个reducer（多个reducer无法保证全局有序）。只有一个reducer，会导致当输入规模较大时，需要较长的计算时间。

sort by：不是全局排序，其在数据进入reducer前完成排序。

distribute by：按照指定的字段对数据进行划分输出到不同的reduce中。

cluster by：除了具有 distribute by 的功能外还兼具 sort by 的功能。

 
