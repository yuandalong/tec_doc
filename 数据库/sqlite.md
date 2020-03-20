# 获取当前时间
```sql
datetime('now', 'localtime')
```
# 建表
```sql
CREATE TABLE database_name.table_name(
   column1 datatype  PRIMARY KEY(one or more columns),
   column2 datatype,
   column3 datatype,
   .....
   columnN datatype,
)
```

# 删除表
```sql
DROP TABLE database_name.table_name;
```

#重命名表
```sql
ALTER TABLE database_name.table_name RENAME TO new_table_name;
```

# 删除字段
不支持，参考修改字段方法

# 修改字段
Sqlite 不支持直接修改字段的名称。
我们可以使用别的方法来实现修改字段名。
1、修改原表的名称
ALTER TABLE table RENAME TO tableOld;
2、新建修改字段后的表
CREATE TABLE table(ID INTEGER PRIMARY KEY AUTOINCREMENT, Modify_Username text not null);
3、从旧表中查询出数据 并插入新表
INSERT INTO table SELECT ID,Username FROM tableOld;
4、删除旧表
DROP TABLE tableOld;