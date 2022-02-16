## Mybatis的缓存机制工作原理
  ### Mybatis一级缓存的工作原理
  
  ![avatar](https://img-blog.csdn.net/20150726164134583?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
  
  第一次发起查询用户ID为1的用户信息，先去找缓存中是否有ID为1的用户信息，如果没有，从数据库查询用户信息。得到用户信息存储到一级缓存中。
  
  如果sqlsession去执行commit操作（插入，修改，删除），清空sqlsession中的一级缓存，这样做的目的为了让换粗中存储的最新的信息，避免脏读。
  
  第二次发起查询用户id为1的用户信息，先去找缓存中是否为1的用户信息，缓存中有，直接从缓存中获取用户信息。
  
  **如果执行两次service调用查询相同的用户信息，不走一级缓存，因为Service方法结束，sqlSession就关闭了，一级缓存就清空。**
 
 ### Mybatis二级缓存的工作原理
 
 ![avatar](https://img-blog.csdn.net/20150726164234783?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
 
 首先开启mybatis的二级缓存。
 
  sqlSession1去查询用户ID为1的用户信息，查询到用户信息会将查询数据存储到二级缓存中。
  
  **如果SqlSession3去执行相同的mapper下的sql，执行commit提交，清空mapper下的二级缓存区域的数据。**
  
  sqlSession2去查询用户id为1的用户信息，去缓存中找是否存在数据，如果存在直接从缓存中取出数据。

### 一级缓存和二级缓存的区别
  二级缓存的范围更大，多个sqlsession可以共享一个userMapper的二级缓存区域。数据类型仍然为HashMap；
  
  userMapper有一个二级缓存区域 **（按namespace分，如果namespace相同则使用同一个相同的二级缓存区）** 其它mapper也有自己的二级缓存区域（按namespace分）
  
  每一个namespace的mapper都有一个二级缓存区域，两个mapper的namespace如果相同，这两个mapper执行sql查询导数据将存在相同的二级缓存区域中；

###  useCache配置禁用二级缓存
  在statement中设置userCache=false可以禁用当前select语句的二级缓存，即每次查询都会发出sql去查询，默认情况true,即sql使用二级缓存。
  
      <select id="getallinfo" resultType="dao.entity.PhoneInfoEntity" userCache="false">
  
  总结：针对每次查询都需要更新的数据sql，要设置成userCache=false, 禁用二级缓存；
  
### Mybatis刷新缓存（就是清空缓存）
  在mapper的同一个namespace中，如果有其他insert，update，detele操作数据后需要刷新缓存，如果不执行刷新换从就会出现脏读；
  在statement中设置 flushCache="true" 属性，默认情况下为true,改成false则不会刷新换存，使用缓存时，如果手动修改数据库表中的查询数据会出现脏读。
  
     <select id="getallinfo" resultType="dao.entity.PhoneInfoEntity" userCache="false">
     
  总结：**一般下执行完commit操作都需要刷新缓存，flushCache=true表示刷新缓存默认情况下为true,我们不用去设置它，这样可以避免数据库脏读。**
  
### 二级缓存的应用场景
  **二级缓存适用于访问多的查询请求且用户对查询结果实时性要求不高** 这种情况可以采用mybatis二级缓从技术降低数据库访问量，提高访问速度；
  
  业务场景比如：耗时较高的统计分析sql，电话账单查询sql等。
  
  实现方法： 通过设置刷新间隔时间，由mybatis每隔一段时间自动清空缓存，根据数据变化频率设置缓存刷新间隔flushInterval，比兔设置为30分钟，60分钟，24小时。根据需求定
  
      <cache eviction = "FIFO" flushInterval="6000" size="512" readOnly="true"/>
      
   flushInterval -- 刷新间隔； 默认不设置，没有刷新间隔； 设置成正整数，代表毫秒形式的间隔。
   
   size -- 引用数目； 可以设置为任意正整数，要记住你缓存的对象数目和你运行的可用内存资源数目，默认是1024
   
   readOnly -- 只读； 属性可以被设置为true或false。只读的缓存会给所有调用者返回缓存对象的相同实例；

   LRU – 最近最少使用的:移除最长时间不被使用的对象。
   
   FIFO – 先进先出:按对象进入缓存的顺序来移除它们。
   
   SOFT – 软引用:移除基于垃圾回收器状态和软引用规则的对象。
   
   WEAK – 弱引用:更积极地移除基于垃圾收集器状态和弱引用规则的对象。
