# Mybatis缓存
## MyBatis缓存介绍
正如大多数持久层框架一样，MyBatis 同样提供了一级缓存和二级缓存的支持

* 一级缓存: 基于PerpetualCache 的 HashMap本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该Session中的所有 Cache 就将清空。
* 二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap存储，不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。

对于缓存数据更新机制，当某一个作用域(一级缓存Session/二级缓存Namespaces)的进行了 C/U/D 操作后，默认该作用域下所有 select 中的缓存将被clear。

### Mybatis一级缓存测试

```java
package me.gacl.test;

import me.gacl.domain.User;
import me.gacl.util.MyBatisUtil;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

/**
 * @author gacl
 * 测试一级缓存
 */
public class TestOneLevelCache {
    
    /*
     * 一级缓存: 也就Session级的缓存(默认开启)
     */
    @Test
    public void testCache1() {
        SqlSession session = MyBatisUtil.getSqlSession();
        String statement = "me.gacl.mapping.userMapper.getUser";
        User user = session.selectOne(statement, 1);
        System.out.println(user);
        
        /*
         * 一级缓存默认就会被使用
         */
        user = session.selectOne(statement, 1);
        System.out.println(user);
        session.close();
        /*
         1. 必须是同一个Session,如果session对象已经close()过了就不可能用了 
         */
        session = MyBatisUtil.getSqlSession();
        user = session.selectOne(statement, 1);
        System.out.println(user);
        
        /*
         2. 查询条件是一样的
         */
        user = session.selectOne(statement, 2);
        System.out.println(user);
        
        /*
         3. 没有执行过session.clearCache()清理缓存
         */
        //session.clearCache(); 
        user = session.selectOne(statement, 2);
        System.out.println(user);
        
        /*
         4. 没有执行过增删改的操作(这些操作都会清理缓存)
         */
        session.update("me.gacl.mapping.userMapper.updateUser",
                new User(2, "user", 23));
        user = session.selectOne(statement, 2);
        System.out.println(user);
        
    }
}
```
### Mybatis二级缓存测试

1. 开启二级缓存，在userMapper.xml文件中添加如下配置
```xml
<mapper namespace="me.gacl.mapping.userMapper">
<!-- 开启二级缓存 -->
<cache/>
```

2. 测试二级缓存

    ```java
    package me.gacl.test;
    
    import me.gacl.domain.User;
    import me.gacl.util.MyBatisUtil;
    import org.apache.ibatis.session.SqlSession;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.junit.Test;
    
    /**
     * @author gacl
     * 测试二级缓存
     */
    public class TestTwoLevelCache {
        
        /*
         * 测试二级缓存
         * 使用两个不同的SqlSession对象去执行相同查询条件的查询，第二次查询时不会再发送SQL语句，而是直接从缓存中取出数据
         */
        @Test
        public void testCache2() {
            String statement = "me.gacl.mapping.userMapper.getUser";
            SqlSessionFactory factory = MyBatisUtil.getSqlSessionFactory();
            //开启两个不同的SqlSession
            SqlSession session1 = factory.openSession();
            SqlSession session2 = factory.openSession();
            //使用二级缓存时，User类必须实现一个Serializable接口===> User implements Serializable
            User user = session1.selectOne(statement, 1);
            session1.commit();//不懂为啥，这个地方一定要提交事务之后二级缓存才会起作用
            System.out.println("user="+user);
            
            //由于使用的是两个不同的SqlSession对象，所以即使查询条件相同，一级缓存也不会开启使用
            user = session2.selectOne(statement, 1);
            //session2.commit();
            System.out.println("user2="+user);
        }
    }
    ```
1. 二级缓存补充说明

    1. 映射语句文件中的所有select语句将会被缓存。
    2. 映射语句文件中的所有insert，update和delete语句会刷新缓存。
    3. 缓存会使用Least Recently Used（LRU，最近最少使用的）算法来收回。
    4. 缓存会根据指定的时间间隔来刷新。
    5. 缓存会存储1024个对象
    
1. cache标签常用属性：
```xml
<cache 
eviction="FIFO"  <!--回收策略为先进先出-->
flushInterval="60000" <!--自动刷新时间60s-->
size="512" <!--最多缓存512个引用对象-->
readOnly="true"/> <!--只读-->
```

# insert返回主键
## xml形式
方法1 推荐
```xml
<!-- 
		所有数据库通用，插入成功返回最近一次插入的id
		它会将id直接赋值到对应的实体当中
			TStudent stu = new TStudent();
			studentMapper.add(TStudent );
			int pk = stu.getId(); // 这就是我们的主键id
 -->
<insert id="add" parameterType="TStudent" useGeneratedKeys="true" keyProperty="id">
  insert into TStudent(name, age) values(#{name}, #{age})
</insert>
```

方法2
```xml
<!-- 注意 keyProperty 属性，selectKey 标签，主键是id -->
<insert id="insertEstimate" parameterType="java.util.Map" useGeneratedKeys="true" keyProperty="id">
	<!-- 获取最近一次插入记录的主键值的方式 -->
	<selectKey resultType="java.lang.Integer" order="AFTER" keyProperty="id">
		SELECT @@IDENTITY
	</selectKey>
	insert into test_table(estimate_no) values(#{budgetNo})
</insert>	
```

## 注解形式
结合Options注解使用
```java
@Insert("insert into nxqf_user(username,nickname,password,phone,email,created,updated) values(#{user.username},#{user.nickname},#{user.password},#{user.phone},#{user.email},#{user.created},#{user.updated})")
@Options(useGeneratedKeys=true, keyProperty="user.id", keyColumn="id")
Integer addUser(@Param("user") User user);
```
如果传入得参数是个对象的时候，这时候我们的keyProperty这个属性值一定要设置成user.id，这样的话，我们插入数据后，主键的值就会自动插入到我们的user对象中去了

# save or update

```sql
-- 设置好表的主键之后使用ON DUPLICATE KEY UPDATE，当主键冲突时触发update
INSERT INTO rmw_column_info 
(dataSource_id, dataSource_siteId, column_id, column_name) 
VALUES(#{1,2,3,'aa') 
ON DUPLICATE KEY UPDATE column_name = 'aa'
```

# springboot + mybatis
参考[githup项目](https://github.com/yuandalong/spring-boot-examples/tree/master/spring-boot-mybatis)

要点：
1. pom加mybatis的starter
    
    ```xml
    <dependency>
          <groupId>org.mybatis.spring.boot</groupId>
          <artifactId>mybatis-spring-boot-starter</artifactId>
          <version>2.0.0</version>
    </dependency>
    ```
2. 设置@MapperScan或者@Mapper
    1. 这俩注解使用一种就可以了
    2. @MapperScan用在启动类或者多数据源项目的DataSourceConfig类，需指定Dao接口所在的package
    3. @Mapper用在Dao接口里
3. 开启表字段名到实体类驼峰属性名映射功能
4. 开启日志打印
    
    ```
    mybatis:
      configuration:
        #开启驼峰映射
        map-underscore-to-camel-case: true
        #开启日志
        log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
    ```
    
    # 注解复杂查询
    
```java
    @Select( " <script>" +
            " select id, user_id userId, batch_number batchNumber, unit_name unitName, word_detail wordDetail,word, score, create_time createTime " +
            " from word_practice_records where user_id =#{userId} and batch_number=#{batchNumber} and unit_name=#{unitName} and  word  in "+
            " <foreach collection='wordScoreViewList' open='(' item='wordScore' separator=',' close=')'> #{wordScore.word}</foreach> "+
            " </script>" )
    List<WordPracticeRecords> getLessThan40WordByWordList(@Param("userId") String userId, @Param( "batchNumber" )String batchNumber,
                                                          @Param( "unitName" ) String unitName, @Param( "wordScoreViewList" ) List<WordScoreView>  wordScoreViewList);

```

**注意：**
需要再前后增加<script></script> 标签

循环使用：

```xml
<foreach collection='wordScoreViewList' open='(' item='wordScore' separator=',' close=')'> 
    #{wordScore.word}
</foreach>
```

 collection 遍历的类型,(集合为list,数组为array,如果方法参数是对象的某个属性,而这个属性是list,或array类型,就可以写形参的名字)
 open 条件的开始 
 close 条件的结束 
 item  遍历集合时候定义的临时变量，存储当前遍历的每一个值 
 separator 多个值之间用逗号拼接
  #{wordScore.word}   获取遍历的每一个值,与item定义的临时变量一致，item变量是一个实体，要获取里面word属性

 