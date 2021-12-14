## Mybatis的核心类：

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
    Executor（执行器）接口有两个实现类，其中BaseExecutor有三个继承类分别是BatchExecutor（重用语句并执行批量更新），ReuseExecutor
    （重用预处理语句prepared statement，跟Simple的唯一区别就是内部缓存statement）,SimpleExecutor（默认，每次都会创建新的statement）
    以上三个就是主要的Executor。通过下图可以看到Mybatis在Executor的设计上面使用了装饰器模式，我们可以用CachingExecutor来装饰前
    面的三个执行器目的就是用来实现缓存。
    
  ### MappedStatement：
    MappedStatement就是用来存放我们SQL映射文件中的信息包括sql语句，输入参数，输出参数等等。一个SQL节点对应一个MappedStatement对象。

## Mybatis 加载步骤：

  ### （1）加载配置文件构建工程类
     SqlSessionFactoryBuilder类的build（）方法中可以看到MyBatis内部定义了一个类XMLConfigBuilder用来解析配置文件myBatis-config.xml。
     针对配置文件中的每一个节点进行解析并将数据存放到Configuration这个对象中（mapperRegistry存放mapper标签的数据），紧接着使用带有
     Configuration的构造方法返回一个工程类DefacutSqlSessionFactory。
  ### （2）通过SqlSessionFactory创建SqlSession
     SqlSessionFactory中会调取openSession的方法，首先拿到之前解析配置文件得到的数据库环境配置，然后生成一个（Executor）执行器，然后
     使用默认的DefaultSqlSession生成一个SqlSession.
  ###  (3) 通过SqlSession拿到Mapper对象的代理
     通过MapperProxyFactory的knowMapper.get(type) type:为UserMapper.生成mapperProxyFactory的单例对象，（生成的mapperProxy对象实现
     了InvovationHandler方法，Serializable.就是JDK动态代理中的方法调用处理器），通过mapperProxy对象生成一个Mapper的代理
  ### （4）通过MapperProxy调用Mapper中的相应方法；
     
