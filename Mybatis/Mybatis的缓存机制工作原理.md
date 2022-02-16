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
    userMapper有一个二级缓存区域**（按namespace分，如果namespace相同则使用同一个相同的二级缓存区）** 其它mapper也有自己的二级缓存区域（按namespace分）
    每一个namespace的mapper都有一个二级缓存区域，两个mapper的namespace如果相同，这两个mapper执行sql查询导数据将存在相同的二级缓存区域中；
