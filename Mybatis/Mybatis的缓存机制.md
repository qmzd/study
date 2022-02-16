## MyBatis的缓存机制

### 什么是查询缓存？
  mybatis提供查询缓存，用于减轻数据压力，提高数据库性能；
  mybatis提供一级缓存，和二级缓存。
  ![avatar](https://img-blog.csdn.net/20150726164148424?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
 
## 一级缓存
  一级缓存是sqlsession级别的缓存，在操作数据库时需要构造sqlsession对象，在对象中有一个（内存区域）数据结构（HashMap）用于存储缓存数据，不同的sqlSession之间的缓存数据区域（hashMap）是相互不影响的。
  
   一级缓存的作用域是同一个sqlsession，在同一个sqlsession中两次执行相同的sql语句，**第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次会从缓存中获取数据将不再从数据库查询，从而提高查询效率。** 当一个sqlsession结束后该sqlsession中的一级缓存也就不存在了。mybatis默认开启一级缓存。
  
  
  **一级缓存为何会失效** 
  [https://blog.csdn.net/qq_39809458/article/details/113993530?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-11-113993530.pc_agg_new_rank&utm_term=SqlSession%E4%BB%80%E4%B9%88%E6%97%B6%E5%80%99%E5%85%B3%E9%97%AD&spm=1000.2123.3001.4430](url)
  
  
  一级缓存只是相对于同一个SqlSession而言。所以在参数和SQL完全一样的情况下，我们使用同一个SqlSession对象调用一个Mapper方法，往往只执行一次SQL，因为使用SqlSession第一次查询后，MyBatis会将其放在缓存中，以后在查询的时候，如果没有声明需要刷新，并且缓存没有超时的情况下，SqlSession都会取出当前的缓存数据，而不会再次发送Sql到数据库。
  
  通过源码我们知道每次SqlSession（准确的说是DefaultSqlSession）的创建都会有一个Transaction事物对象的生成。也就是说：
  1. 一个事物Transaction对象与一个SqlSession对象时一一对应的关系；
  2. 同一个SqlSession不管执行多少次数据库操作，只要是没有执行close，那么整个操作都是同一个Transaction中执行的。

![avatar](https://img-blog.csdnimg.cn/20201012134836126.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNzM4MTU=,size_16,color_FFFFFF,t_70)

### 一级缓存的生命周期有多长？
  a.mybatis在开启一个数据库会话时，会创建一个新的sqlSession对象，SqlSession对象中会有一个新的Executor对象，Executor对象中持有一个新的PerpetualCache对象；当回话结束时，SqlSessioN对象及其内部的Exceutor对象还有PerpetualCache对象也一并释放掉。
  
  b.如果SqlSession调用了close（）方法，会释放掉一级缓存PerpetualCache对象，一级缓存将不可用；
  
  c.如果SqlSession调用了clearCache()，会清空PerpetualCache对象中的数据，但是该对象仍可使用；
  
  d.SqlSession中执行了任何一个update操作（update,delete,insert）都会清空perpetualCache对象的数据，但该对象可以继续使用；
  
### 如何判断两次查询是完全相同的查询？
  mybatis认为，对于两次查询，如果一下条件都完全一样，那么就认为他们是完全相同的两次查询。
    1. 传入的statementID；
    2. 查询时要求的结果集中的结果范围；---结果集
    3.这次查询所产生的最终要传递的JDBC java.sql.Preparedstatement的Sql语句字符串（boundSql.getSql()）---sql语句
    4.传递给java.sql.Statement要设置的参数值 ---参数值

## 二级缓存
  mybatis的二级缓存是Application级别的缓存，它可以提高数据库查询的效率，以提高应用的性能。
  mybatis的缓存机制整体设计以及二级缓存的工作模式
  ![avatar](https://img-blog.csdnimg.cn/20201012134820519.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIzNzM4MTU=,size_16,color_FFFFFF,t_70)
  
 二级缓存是Mapper级别的缓存，多个Sqlsession去操作同一个Mapper的Sql语句，多个SqlSession去操作数据库得到的数据会存二级缓存区域，多个SqlSession可以共用二级缓存，**二级缓存是跨SqlSession的**

二级缓存是多个SqlSession共享的，其作用域是mapper的同一个namespace,不同的sqlSession两次执行相同namespace下的sql语句且向sql中传递参数也相同即最终执行相同的sql语句，第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次会从缓存中获取数据将不再从数据库查询，从而提高查询效率。**Mybatis默认没有开启二级缓存**需要在setting全局参数中配置开启二级缓存。

sqlSessionFactory层面上的二级缓存默认是不开启的，二级缓从的开启需要进行配置，实现二级缓存的时候，MyBatis要求返回的POJO必须可是序列化的，也就是要求实现Serializable接口，配置方法很简单，只需要在映射XML文件配置就可以开启缓存了<cache/>，如果我们配置了二级缓存就意味着：
  1. 映射语句文件中的所有select语句将会被缓存；
  2. 映射语句文件中所有的insert,update,dalete语句会刷新缓存；
  3. 缓存会使用默认的Least Recentiy Used （LRU，最近最少使用的）算法来回收。
  4. 根据时间表，比如No Flush Interval,（CNF没有刷新间隔），缓存不会任何时间顺序来刷新
  5. 缓存会存储列表集合或对象（无论查询方法返回什么）的1024个应用
  6. 缓存会被视为是read/write（可读/可写）的缓存，意味着对象检索不是共享的，而且可以安全的被调用者修改，不干扰其他调用者或者线程所做的潜在修改。

如果缓存中有数据，就不用从数据库中获取，大大提高系统性能。

本文内容摘自：[https://blog.csdn.net/u012373815/article/details/47069223](url) 
