### 什么是查询缓存？
  mybatis提供查询缓存，用于减轻数据压力，提高数据库性能；
  mybatis提供一级缓存，和二级缓存。
  
### 一级缓存
  一级缓存是sqlsession级别的缓存，在操作数据库时需要构造sqlsession对象，在对象中有一个（内存区域）数据结构（HashMap）用于存储缓存数据，不同的sqlSession之间的缓存数据区域（hashMap）是相互不影响的。
  一级缓存的作用域是同一个sqlsession，在同一个sqlsession中两次执行相同的sql语句，**第一次执行完毕会将数据库中查询的数据写到缓存（内存），第二次会从缓存中获取数据将不再从数据库查询，从而提高查询效率。** 当一个sqlsession结束后该sqlsession中的一级缓存也就不存在了。mybatis默认开启一级缓存。
  一级缓存为何会失效 [https://blog.csdn.net/qq_39809458/article/details/113993530?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-11-113993530.pc_agg_new_rank&utm_term=SqlSession%E4%BB%80%E4%B9%88%E6%97%B6%E5%80%99%E5%85%B3%E9%97%AD&spm=1000.2123.3001.4430](url)
  

