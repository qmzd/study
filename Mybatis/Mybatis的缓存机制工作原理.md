## Mybatis的缓存机制工作原理
  ### Mybatis一级缓存的工作原理
  
  ![avatar](https://img-blog.csdn.net/20150726164134583?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
  
  第一次发起查询用户ID为1的用户信息，先去找缓存中是否有ID为1的用户信息，如果没有，从数据库查询用户信息。得到用户信息存储到一级缓存中。
  
  如果sqlsession去执行commit操作（插入，修改，删除），清空sqlsession中的一级缓存，这样做的目的为了让换粗中存储的最新的信息，避免脏读。
  
  第二次发起查询用户id为1的用户信息，先去找缓存中是否为1的用户信息，缓存中有，直接从缓存中获取用户信息。
  
