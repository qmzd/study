## 事物的隔离级别

  ### 什么是隔离级别？
    隔离级别是指若干个并发的事务之间的隔离程度；
    一般数据库的隔离级别有4个，由低到高依次是Read uncommitted, Read committed, Repeatable read, Serializable,这四个级别可以逐个解决脏读，不可重复读，幻读这几类问题。
  
  ### Read uncommitted
    可解决：脏读，不可重复读，幻读
    
