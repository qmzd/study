## MyBatis的缓存机制

### 什么是查询缓存？
  mybatis提供查询缓存，用于减轻数据压力，提高数据库性能；
  mybatis提供一级缓存，和二级缓存。
  
### 一级缓存
  一级缓存是sqlsession级别的缓存，在操作数据库时需要构造sqlsession对象，在对象中有一个（内存区域）数据结构（HashMap）用于存储缓存数据，不同的sqlSession之间的缓存数据区域（hashMap）是相互不影响的。
  一级缓存的作用域是同一个sqlsession，在同一个sqlsession中两次执行相同的sql语句，**第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次会从缓存中获取数据将不再从数据库查询，从而提高查询效率。** 当一个sqlsession结束后该sqlsession中的一级缓存也就不存在了。mybatis默认开启一级缓存。
  
  
  **一级缓存为何会失效** 
  [https://blog.csdn.net/qq_39809458/article/details/113993530?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-11-113993530.pc_agg_new_rank&utm_term=SqlSession%E4%BB%80%E4%B9%88%E6%97%B6%E5%80%99%E5%85%B3%E9%97%AD&spm=1000.2123.3001.4430](url)
  
  
  一级缓存只是相对于同一个SqlSession而言。所以在参数和SQL完全一样的情况下，我们使用同一个SqlSession对象调用一个Mapper方法，往往只执行一次SQL，因为使用SqlSession第一次查询后，MyBatis会将其放在缓存中，以后在查询的时候，如果没有声明需要刷新，并且缓存没有超市的情况下，SqlSession都会去除当前的缓存数据，而不会再次发送Sql到数据库。
  
  通过源码我们知道每次SqlSession（准确的说是DefaultSqlSession）的创建都会有一个Transaction事物对象的生成。也就是说：
  1. 一个事物Transaction对象与一个SqlSession对象时一一对应的关系；
  2. 同一个SqlSession不管执行多少次数据库操作，只要是没有执行close，name整个操作都是同一个Transaction中执行的。

### 一级缓存的生命周期有多长？
  a.mybatis在开启一个数据库会话时，会创建一个新的sqlSession对象，SqlSession对象中会有一个新的Executor对象，Executor对象中持有一个新的PerpetualCache对象；当回话结束时，SqlSessioN对象及其内部的Exceutor对象还有PerpetualCache对象也一并释放掉。
  
  b.如果SqlSession调用了close（）方法，会释放掉一级缓存PerpetualCache对象，一级缓存将不可用；
  
  c.如果SqlSession调用了clearCache()，会清空PerpetualCache对象中的数据，但是该对象仍可使用；
  
  d.SqlSession中执行了任何一个update操作（update,delete,insert）都会清空perpetualCache对象的数据，但该对象可以继续使用；

