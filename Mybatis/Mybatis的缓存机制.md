### 什么是查询缓存？
  mybatis提供查询缓存，用于减轻数据压力，提高数据库性能；
  mybatis提供一级缓存，和二级缓存。
  
### 一级缓存
  一级缓存是sqlsession级别的缓存，在操作数据库时需要构造sqlsession对象，在对象中有一个（内存区域）数据结构（HashMap）用于存储缓存数据，不同的sqlSession之间的缓存数据区域（hashMap）是相互不影响的，
