##Mybatis的核心类：

  ### SqlSessionFactory: 
    每一个MyBatis的应用都是以一个SqlSessionFactory的实例为中心的，SqlSessionFactory的实例可以通过SqlSessionFactoryBuilder获得，
    而SqlSessionFactoryBuilder则通过Xml配置文件或通过Java的方式构建出SqlSessionFactory的实例。SqlSessionFactory一旦被创建就用
    应该在应用的运行期间一直存在，建议使用单例模式或者静态代理模式。一个SqlsessionFactory对应配置文件中的一个环境（environment）
    如果纽澳使用多个数据库就配置多个环境分别对应一个SqlSessionFactory。
    
  ### SqlSession:
    SqlSession是一个接口，它是有2个实现类，分别是DefacultSqlSession(默认使用)以及SqlSessionManager。SqlSession通国内部存放的执行
    器（Executor）来对数据进行CRUD操作，此外SqlSession不是线程安全的，因为没一次操作完数据库后都要调用close对其进行关闭，官方建议
    通过try-finally来保证总是关闭SqlSession。
    
  ### Exector:
    Executor（执行器）接口有两个实现类，其中BaseExecutor有三个继承类分别是BatchExecutor（重用语句并执行批量更新）处理语句prepared statement, 跟Simple的唯一区别是内部缓存statement）SimpleExecutor（默认，每次都会创建新个是主要的executor，通过下图可以看到mybatis在Executor的设计上面使用了装饰器模式，我们可以用CachingExecutor来装饰前面三个执的就是用
