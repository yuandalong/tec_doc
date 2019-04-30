## 连接mongo
`mongo ip:port`

## 显示数据库
`show dbs;`

## 使用数据库
`use dbName;`

## 显示表
`show tables;`

## 查询数据
### 查表里所有数据
`db.tableName.find(); //默认查20条`

### 查指定字段
`db.tableName.find({"columnName":"value"});`

### 查大于

```shell
#$gt表示>，lt表示<，gte表示>=，lte表示<=，ne表示!=
db.tableName.find({"columnName" : {$gt: 22}}); 
```

### 字段去重

`db.tableName.distinct("columnName")；`

### 只查指定列数据

`db.tableName.find({},{"columnName":1});//注意参数里是两个json，第一个json是条件，第二个json是显示字段`

### 排序

`db.tableName.find().sort({"columnName":1});//1为升序，-1为降序`

### 查指定条数

`db.tableName.find().limit(5);//查前五条`

### or
`db.tableName.find({$or:[{"columnName":"value"},{"cloumnName2":"value2"}]});//注意or条件是json数组`

### 查一条
会格式化显示
`json：db.tableName.findOne();`

### 查条数
`db.tableName.find({age: {$gte: 25}}).count();// = select count(*) from tableName where age >= 25;`

### 聚合查询

#### sum 

```shell
#select sum(tolStore ) from tableName,_id为group by的字段，没有为null
db.tableName.aggregate([{$group:{_id:null,total:{$sum:"$tolStore"}}}])；
```

#### group

```shell
#match为where条件
db.rptDailyDishSale.aggregate([{$match:{'storeId':'cbe34014-cfd3-477b-9344-6c1c951bc8ca','bussDate':'2016-11-08'}},{$group:{_id:'$dishName'}}])
```

#### sum和group组合使用 
     
```shell
#分组完之后查总记录条数，如果查每个记录的条数的话就在第一个group的json里加sum
db.rptDailyDishSale.aggregate([{$match:{'storeId':'cbe34014-cfd3-477b-9344-6c1c951bc8ca','bussDate':'2016-11-08'}},{$group:{_id:'$dishName'}},{$group:{_id:null,a:{$sum:1}}}])

#指定字段sum
db.rptDailyBussStat.aggregate([{ $match:{'bussDate':'2016-09-11'}},{$group: { _id:null,transTime:{ $sum:'$transTime'}}}])
```

### 是否存在指定字段的数据
`db.accBillMongo.find({'payList':{$exists:true}})` 
注意exists表达式不能带引号，否则就是查字段值等于表达式字符串的数据了
     
### 模糊查询 

```shell
#like ‘A%' 冒号后面不带引号
db.UserInfo.find({'userName':/^A/}) 
#like ‘%A%'  
db.UserInfo.find({'userName':/A/})   
```
     
### 查最大最小值 

结合sort和limit(1)
```shell
#sort -1倒叙 1正序
db.rptDailyPrefStat.find().sort({'bussDate':-1}).limit(1)
```

### 去重

`db.dimStore.distinct('tenantId’)`

#### 去重后计算条数

`db.dimStore.distinct('tenantId').length`

#### 带条件去重

`db.runCommand({"distinct":”表名","key":”去重字段","query":{"time":/^2011-12-15/}}).values.length`

## 更新数据

`db.collection.update( criteria, objNew, upsert, multi );`
* criteria : update的查询条件，类似sql update查询内where后面的
* objNew   : update的对象和一些更新的操作符（如$,$inc...）等，也可以理解为sql update查询内set后面的
* upsert   : 这个参数的意思是，如果不存在update的记录，是否插入objNew,true为插入，默认是false，不插入。
* multi    : mongodb默认是false,只更新找到的第一条记录，如果这个参数为true,就把按条件查出来多条记录全部更新。

例：

```shell
#只更新了第一条记录
db.test0.update( { "count" : { $gt : 1 } } , { $set : { "test2" : "OK"} } ); 
#全更新
db.test0.update( { "count" : { $gt : 3 } } , { $set : { "test2" : "OK"} },false,true ); 
#没查到匹配数据后，只添加一条
db.test0.update( { "count" : { $gt : 4 } } , { $set : { "test5" : "OK"} },true,false ); 
#没查到匹配数据后，全加进去了，没啥屁用，都没有匹配数据了就算全加进去也是加一条
db.test0.update( { "count" : { $gt : 5 } } , { $set : { "test5" : "OK"} },true,true ); 
#全更新
db.test0.update( { "count" : { $gt : 15 } } , { $inc : { "count" : 1} },false,true );
#只更新第一条
db.test0.update( { "count" : { $gt : 10 } } , { $inc : { "count" : 1} },false,false );
```

## 删除数据
        
```shell
#清表
db.tableName.drop();
#根据查询条件删数据
db.tableName .remove({"columnName ": "value"});
```

## 备份数据

`mongodump -d test -o data/backup`

参数说明：
* -h:指明数据库宿主机的IP
* -u:指明数据库的用户名
* -p:指明数据库的密码
* -d:指明数据库的名字
* -c:指明collection的名字
* -o:指明到要导出的文件名
* -q:指明导出数据的过滤条件

## 还原数据

`mongorestore -d test --drop data/backup/test/`

参数说明：
* -h:指明数据库宿主机的IP
* -u:指明数据库的用户名
* -p:指明数据库的密码
* -d:指明数据库的名字
* -c:指明collection的名字
* -o:指明到要备份的文件名
* -q:指明备份数据的过滤条件

## 导出指定表数据

```shelll
mongoexport -h 192.168.49.96:40001 -u cloud_rpt -p cloud_rpt -d cloud_rpt -c accBillMongo -o ~/bill.json --type json
```

## 启动服务
`mongod -f /yazuo/data/mongodb/mongo.conf`  

-f为使用配置文件，mongo.conf为配置文件，不使用配置文件的话可以使用各个启动参数

mongo.conf内容包括

```shell
dbpath=/yazuo/data/mongodb
logpath=/yazuo/logs/mongodb/mongo30000.log
port=30000
logappend=true
fork=true
journal=true
```

##当前数据库连接数查询

`db.serverStatus().connections`
     
## Spring mongoTemplate常用方法

```java
//分组查询条数,如果查所有的条数则group字段随便传一个不存在的，如group(“1")
Aggregation aggregation = Aggregation. newAggregation(Aggregation .match( c),Aggregation. group("storeStat" , "storeProp" ).count().as("count" ));
AggregationResults<HashMap> aggRes = this .getMongoTemplate().aggregate(aggregation , "dimStore" , HashMap. class);
List<HashMap > listRes = aggRes .getMappedResults();

//分组求和
List<Map<String, Object>> result = new ArrayList<>();
List<AggregationOperation> aggList = new ArrayList<>();
 if (StringUtils.isNotBlank( startDate))
     aggList .add(Aggregation.match(Criteria. where( "bussDate").lte( endDate ).gte(startDate )));
 if (storeIds != null && storeIds .length > 0)
     aggList .add(Aggregation.match(Criteria. where( "storeId").in( storeIds )));
 aggList .add(Aggregation.match(Criteria. where( "saleMode").lt(888)));
 aggList .add(Aggregation.group( "storeId" , "payId" ).sum( "payAmount" ).as("payAmount" )
       . sum( "overAmount").as( "overAmount" ));
 aggList .add(Aggregation.project( "storeId" , "payId" , "payAmount" , "overAmount" ));
AggregationOperation[] aoArray = new AggregationOperation[aggList .size()];
 for (int i = 0; i < aggList.size(); i ++)
    aoArray [i ] = aggList.get( i);
Aggregation aggregation = Aggregation.newAggregation( aoArray);
AggregationResults<HashMap> aggRes = this .getMongoTemplate().aggregate(aggregation ,
"rptDailyPayStat" , HashMap.class );
List<HashMap> list = aggRes .getMappedResults();


//简单查询，不需要分组
Criteria c = Criteria.where( "_id"). is (dishId );
         Query query = new Query ();
         query.addCriteria ( c);
         List <DimDish > dishList = mongoTemplate .find ( query, DimDish .class , "dimDish");
         if (dishList != null && dishList .size () > 0 ){
             return dishList .get ( 0). getDishName ();
        }
         else {
             return null ;
        }


//更新
Query queryCondition = new Query ();
             queryCondition .addCriteria ( Criteria. where( "bussDate"). is (tem . getBussDate())
                    . and ("storeId" ). is( tem .getStoreId ()). and( "saleMode" ).is ( tem. getSaleMode ()));

             logger. debug( "查询条件为：{}" , JSON .toJSONString( queryCondition .getQueryObject ()));
             // 做修改操作
             Update update = new Update ();

             update .set ( "bussDate", tem .getBussDate ());
             
             update .inc ( "membZeroGuests", tem .getMembZeroGuests ());
             //未计收入金额在RptDailyPayStatMongoImpl中计算
             // 做修改操作
             mongoTemplate. upsert( queryCondition , update , RptDailyBussStat . class);

//模糊查询
Criteria.where("storeName").regex(storeName)

```