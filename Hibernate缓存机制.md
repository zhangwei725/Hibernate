## Hibernate缓存机制

## 一. 作用

	Hibernate是一个持久层框架，经常访问物理数据库，为了降低应用程序对物理数据源访问的频次，从而提高应用程序的运行性能。缓存内的数据是对物理数据源中的数据的复制，应用程序在运行时从缓存读写数据，在特定的时刻或事件会同步缓存和物理数据源的数据
## 二. 分类

	事务范围 Hibernate一级缓存
		Hibernate一级缓存又称为“Session的缓存”，它是内置的，意思就是说，只要你使用hibernate就必须使用session缓存。由于Session对象的生命周期通常对应一个数据库事务或者一个应用事务，因此它的缓存是事务范围的缓存。在第一级缓存中，持久化类的每个实例都具有唯一的OID。 
	应用范围 Hibernate二级缓存
		 Hibernate二级缓存又称为“SessionFactory的缓存”，由于SessionFactory对象的生命周期和应用程序的整个过程对应，因此Hibernate二级缓存是进程范围或者集群范围的缓存，有可能出现并发问题，因此需要采用适当的并发访问策略，该策略为被缓存的数据提供了事务隔离级别。第二级缓存是可选的，是一个可配置的插件，在默认情况下，SessionFactory不会启用这个插件
	集群范围（多SessionFactory） 
	   在集群环境中,缓存被一个机器或多个机器的进程共享,缓存中的数据被复制到集群环境中的每个进程节点,进程间通过远程通信来保证缓存中的数据的一致,缓存中的数据通常采用对象的松散数据形式
## 三.什么样的数据适合存放到第二级缓存中

	很少被修改的数据。 
	不是很重要的数据，允许出现偶尔并发的数据。 
	不会被并发访问的数据。 
	常量数据。 
	不会被第三方修改的数据 　
## 四.不适合存放到第二级缓存的数据

	1 经常被修改的数据 　　
	2 绝对不允许出现并发访问的数据，如财务数据，绝对不允许出现并发 　　
	3 与其他应用共享的数据
## 五. Hibernate查找对象如何应用缓存？

	当Hibernate根据ID访问数据对象的时候，首先从Session一级缓存中查；查不到，如果配置了二级缓存，那么从二级缓存中查；如果都查不到，再查询数据库，把结果按照ID放入到缓存删除、更新、增加数据的时候，同时更新缓存
## 六. Hibernate管理缓存实例

	无论何时，我们在管理Hibernate缓存（Managing the caches）时，当你给save()、update()或saveOrUpdate()方法传递一个对象时，或使用load()、 get()、list()、iterate() 或scroll()方法获得一个对象时, 该对象都将被加入到Session的内部缓存中。 当随后flush()方法被调用时，对象的状态会和数据库取得同步。 如果你不希望此同步操作发生，或者你正处理大量对象、需要对有效管理内存时，你可以调用evict() 方法，从一级缓存中去掉这些对象及其集合
## 七. 不使用二级缓存的方法

Hibernate的各种查询方式中，以下几种方式不使用缓存，直接从数据库读写：

- get()
- find()
- list()

其中后两者在使用HibernateTemplate时均为find()方法。但当开启了查询缓存后，使用这些方法时，同样也会把查询的结果存入缓存，这会造成一定的时间消耗，但是可以有效的避免使用缓存时的N+1问题。

## 4.2. 使用二级缓存的方法

Hibernate的以下方法使用二级缓存

- load()
- iterate()



二级缓存操作步骤

2.2.1、 二级缓存配置

1. 导入jar ehcache

   ```
   <!-- https://mvnrepository.com/artifact/org.hibernate/hibernate-ehcache -->
   <dependency>
       <groupId>org.hibernate</groupId>
       <artifactId>hibernate-ehcache</artifactId>
       <version>5.2.10.Final</version>
   </dependency>
   <dependency>
   	<groupId>net.sf.ehcache</groupId>
   	<artifactId>ehcache</artifactId>
   	<version>2.10.3</version>
   </dependency>

   ```

2. 在hibernate.cfg.xml中开启二级缓存

   ```
   <propertyname="hibernate.cache.use_second_level_cache">true</property>
   ```

3. 配置二级缓存技术提供商

   ```
   <property name="cache.region.factory_class">org.hibernate.cache.EhCacheRegionFactory</property>
   ```

4. 二级缓存配置文件的位置 

       <property name="hibernate.cache.provider_configuration_file_resource_path">ehcache.xml</property>

5. 配置缓存数据对象并发策略

   ```
   在类上使用
   @Cache(usage=CacheConcurrencyStrategy.READ_ONLY)
   在cfg中注册
   <class-cache usage="read-write" class="包名类名"/>  
   <collection-cache usage="read-write" collection="包名.类名.集合属性"/>  
   ```

6. 添加二级缓存配置文件到resource文件夹下

   ```
       <!--
          默认缓存配置，如果类没有做特定的设置，则使用这里配置的缓存属性。
    
           maxElementsInMemory  - 设置缓存中允许保存的最大对象（pojo）数量
           eternal           - 设置对象是否永久保存，如果为true，则缓存中的数据永远不销毁，一直保存。
           timeToIdleSeconds - 设置空闲销毁时间。
                          只有eternal为false时才起作用。表示从现在到上次访问时间如果超过这个值，则缓存数据销毁
           timeToLiveSeconds - 设置活动销毁时间。表示从现在到缓存创建时间如果超过这个值，则缓存自动销毁
           overflowToDisk    - 设置是否在超过保存数量时，将超出的部分保存到硬盘上。
           -->
   <ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">  
       <diskStore path="java.io.tmpdir"/> 配置二级缓存硬盘临时目录位置   
            <defaultCache    
               maxElementsInMemory="10000" // 内存中最大对象数量 ，超过数量，数据会被缓存到硬盘   
               eternal="false"  
               timeToIdleSeconds="120" // 是否缓存为永久性 false 不永久  
               timeToLiveSeconds="120" // 存活时间，对象不管是否使用，到了时间回收  
               overflowToDisk="true" // 是否可以缓存到硬盘  
               maxElementsOnDisk="10000000" // 硬盘缓存最大对象数量   
               // 当jvm结束时是否持久化对象 true false 默认是false  
               diskExpiryThreadIntervalSeconds="120"  // 指定专门用于清除过期对象的监听线程的轮询时间   
               memoryStoreEvictionPolicy="LRU"   
               />  
      <!-- 指定区域cache：通过name指定，name对应到Hibernate中的区域名即可-->  
     <cache name="cn.javass.h3test.model.UserModel"  
                   eternal="false"  
                   maxElementsInMemory="100"  
                   timeToIdleSeconds="1200"  
                   timeToLiveSeconds="1200"  
                   overflowToDisk="false">  
     </cache>  
   </ehcache>  
   ```

7. ​

   1. 测试代码

   2. 实体类

      ```
      @Entity
      读写缓存权限
      //@Cache(usage=CacheConcurrencyStrategy.READ_WRITE)
      //只读缓存权限
      @Cache(usage=CacheConcurrencyStrategy.READ_ONLY)
      public class User {
          @Id
          @Column(name="id")
          @GenericGenerator
          private Integer userID;
          private String name ;
          private Integer age;
          @Column(name="birth")
          @Temporal(TemporalType.DATE)
          private Date birthday;
          

          public User(){}
          
          public User(Integer userID, String name, Integer age, Date birthday) {
              super();
              this.userID = userID;
              this.name = name;
              this.age = age;
              this.birthday = birthday;
          }

          public Integer getUserID() {
              return userID;
          }
          public void setUserID(Integer userID) {
              this.userID = userID;
          }
          public String getName() {
              return name;
          }
          public void setName(String name) {
              this.name = name;
          }
          public Integer getAge() {
              return age;
          }
          public void setAge(Integer age) {
              this.age = age;
          }
          public Date getBirthday() {
              return birthday;
          }
          public void setBirthday(Date birthday) {
              this.birthday = birthday;
          }

          @Override
          public String toString() {
              return "User [userID=" + userID + ", name=" + name + ", age=" + age
                      + ", birthday=" + birthday + "]";
          }
          
      }
      ```

   3. ​    二级缓存ecache：

      ```
      <?xml version="1.0" encoding="UTF-8"?>
      <!-- 
            maxEntriesLocalHeap： 在内存中缓存的element的最大数目。 
            maxEntriesLocalDisk： 在磁盘上缓存的element的最大数目，默认值为0，表示不限制。 
            eternal： 设定缓存的elements是否永远不过期。如果为true，则缓存的数据始终有效，
                        如果为false那么还要根据timeToIdleSeconds，timeToLiveSeconds判断。 
            <persistence strategy="localTempSwap"/>  内存存满后将数据存入硬盘
            timeToIdleSeconds="10"  缓存空闲时间   默认值0 一直存活
            timeToLiveSeconds="15"  缓存最大存活时间    默认值0 一直存活
            diskExpiryThreadIntervalSeconds:磁盘数据的有效时间
            memoryStoreEvictionPolicy="LFU"
                FIFO ，first in first out (先进先出).
              LFU ， Less Frequently Used (最少使用).意思是一直以来最少被使用的。缓存的元素有一个hit 属性，hit 
                     值最小的将会被清出缓存。
              LRU ，Least Recently Used(最近最少使用). (ehcache 默认值).缓存的元素有一个时间戳，当缓存容量满了，
                      而又需要腾出地方来缓存新的元素的时候，那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
       -->

      <ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:noNamespaceSchemaLocation="ehcache.xsd">

          <!-- <diskStore path="java.io.tmpdir"/> -->
          <diskStore path="E:\\cache4"/>
          <defaultCache
                  maxEntriesLocalHeap="2"
                  eternal="false"
                  timeToIdleSeconds="120"
                  timeToLiveSeconds="120"
                  diskSpoolBufferSizeMB="30"
                  maxEntriesLocalDisk="10000000"
                  diskExpiryThreadIntervalSeconds="120"
                  memoryStoreEvictionPolicy="LRU">
              <persistence strategy="localTempSwap"/>
          </defaultCache>
      </ehcache>
      ```

   4.   hibernate配置文件：

      ```
      <?xml version="1.0" encoding="utf-8"?>
        <!-- 
            文档约束：DTD  Document Type Definition
                           ：标签，属性，层级，先后顺序
         -->
        <!DOCTYPE hibernate-configuration PUBLIC
            "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
            "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
        <hibernate-configuration>
            <session-factory>
                <!-- 数据库连接相关 -->
                <property name="hibernate.connection.url">com.mysql.jdbc.Driver</property>
                <property name="hibernate.connection.driver_class">oracle.jdbc.OracleDriver</property>
                <property name="hibernate.connection.username">root</property>
                <property name="hibernate.connection.password">root</property>
                <!-- 最大连接数 -->
                <property name="hibernate.c3p0.max_size">3</property>
                <!-- 最下连接数 -->
                <property name="hibernate.c3p0.min_size">1</property>
                <!-- 获取链接等待超时时间： 毫秒-->
                <property name="checkoutTimeout">3000</property>
                <!-- 指示连接池的类型 -->
                <property name="hibernate.connection.provider_class">org.hibernate.connection.C3P0ConnectionProvider</property>
                <!-- hibernate自身配置信息 
                            方言：指示数据库种类，便于hibernate对不同的数据库做出相应的适应。
                -->
                <property name="hibernate.dialect">org.hibernate.dialect.Oracle10gDialect</property>
                <!-- 控制台打印出sql语句 -->
                <property name="hibernate.show_sql">true</property>
                <!-- 格式化sql语句 -->
                <property name="hibernate.format_sql">true</property>
                <!-- 禁用掉javaEE6的bean-validator -->
                <property name="javax.persistence.validation.mode">none</property>
                <!-- getCurrentSession -->
                <property name="hibernate.current_session_context_class">thread</property>
                <!-- 开启二级缓存 -->
                <property name="hibernate.cache.use_second_level_cache">true</property>
                <!-- 二级缓存类别：EhCache,OSCache，JbossCache -->
                <property name="hibernate.cache.provider_class">org.hibernate.cache.EhCacheProvider</property>
                <!-- 开启查询缓存 -->
                <property name="hibernate.cache.use_query_cache">true</property>
                <!-- 映射信息的注册 -->
                <mapping class="User"></mapping>
            </session-factory>
        </hibernate-configuration>


      ```

   5. 一级缓存测试

      ```
        public class TestCache {

            /**
             * 一级缓存测试:不能跨事务
             */
            @Test
            public void testFirstLevelCache(){
                Session session=HibernateUtil.getCurrentSession();
                Transaction tx=session.beginTransaction();
                //1.检查缓存，没有可用的数据，转向数据库，查询出的数据，进入一级缓存
                User user=(User)session.get(User.class,1);
                System.out.println(user);
                user.setAge(41);
                //2.再次发起同样的查询,检查缓存，有可用的数据，则不在查询数据库。
                User user2=(User)session.get(User.class,1);
                System.out.println(user2);
                tx.commit();
                
                /*Session session2=HibernateUtil.openSession();
                Transaction tx2=session2.beginTransaction();
                User user3=(User)session2.get(User.class,109);
                System.out.println(user3);
                tx2.commit();
                session2.close();*/
            }
            /**
             * 一级缓存测试：HQL语句执行查询不能检查缓存
             */
            @Test
            public void testFirstLevelCache2(){
                Session session=HibernateUtil.getCurrentSession();
                Transaction tx=session.beginTransaction();
                //1.检查缓存，没有可用的数据，转向数据库，查询出的数据，进入一级缓存
                User user=(User)session.get(User.class,1);
                //2.list和uniqueResult不能检查缓存，直接转向数据库。
                String hql="from User u where u.name='zhangjifeng'";
                Query query=session.createQuery(hql);
                User user2=(User)query.uniqueResult();
                System.out.println(user);
                System.out.println(user2);
                tx.commit();
            }
            /**
             * 一级缓存测试：iterate()：
             */
            @Test
            public void testFirstLevelCache3(){
                Session session=HibernateUtil.getCurrentSession();
                Transaction tx=session.beginTransaction();
                //1.HQL查询多条数据，数据进入一级缓存
                String hql="from User u where u.name like ?";
                Query query=session.createQuery(hql);
                query.setString(0,"ji%");
                query.list();
                
                String hql2="from User u where u.name like ?";
                Query query2=session.createQuery(hql);
                query2.setString(0,"ji%");
                //迭代器中存储ID
                //只查询ID，通过ID检查缓存。
                Iterator it=query2.iterate();
                while(it.hasNext()){
                    User user=(User)it.next();
                    System.out.println(user);
                }
                tx.commit();
            }
      ```

   6. 二级缓存测试

      ```
       /**
             * 二级缓存测试
             */
            @Test
            public void testSecondLevelCache(){
                Session session=HibernateUtil.openSession();
                Transaction tx=session.beginTransaction();
                User user=(User)session.get(User.class,1);
                User user3=(User)session.get(User.class,2);
                System.out.println(user);
                tx.commit();
                session.close();
                
                Session session2=HibernateUtil.openSession();
                Transaction tx2=session2.beginTransaction();
                
                User user2=(User)session2.get(User.class,1);
                user2.setName("second222");
                System.out.println(user2);
                tx2.commit();
                session2.close();
            }
            /**
             * 查询缓存测试
             */
            @Test
            public void testQueryCache(){
                Session session=HibernateUtil.openSession();
                Transaction tx=session.beginTransaction();
                String hql="select u.name,u.age from User u where u.age>=?";
                Query query=session.createQuery(hql);
                query.setInteger(0,20);
                //本次查询要使用查询缓存
                query.setCacheable(true);
                //检查查询缓存，没有可用的缓存，则转向数据库，将结果缓存在查询缓存中：{HQL:结果（字段）}
                query.list();
                tx.commit();
                 session.close();
                
        //         Session session3=HibernateUtil.openSession();
        //        Transaction tx3=session3.beginTransaction();
        //         session3.save(new User());
        //        tx3.commit();
        //        session3.close();
                
                Session session2=HibernateUtil.openSession();
                Transaction tx2=session2.beginTransaction();
                String hql2="select u.name,u.age from User u where u.age>=?";
                Query query2=session2.createQuery(hql2);
                query2.setInteger(0,20);
                //本次查询要使用查询缓存
                query2.setCacheable(true);
                //检查查询缓存，发现可用缓存数据，直接使用，不在查询数据库
                query2.list();
                tx2.commit();
                session2.close();
            }
            /**
             * 查询缓存+二级缓存
             */
            @Test
            public void testQueryCache2(){
                Session session=HibernateUtil.openSession();
                Transaction tx=session.beginTransaction();
                String hql="from User u where u.age>=?";
                Query query=session.createQuery(hql);
                query.setInteger(0,20);
                //本次查询要使用查询缓存
                query.setCacheable(true);
                //检查查询缓存,没有可用数据，则转向数据库，获得一个实体User，将{HQL:User的ID}存入查询缓存
                // 将{User的ID：User}存入二级缓存
                query.list();
                tx.commit();
                 session.close();
                 Session session2=HibernateUtil.openSession();
                Transaction tx2=session2.beginTransaction();
                String hql2="from User u where u.age>=?";
                Query query2=session2.createQuery(hql2);
                query2.setInteger(0,20);
                //本次查询要使用查询缓存
                query2.setCacheable(true);
                //检查查询缓存，获得缓存数据：User的ID,通过UserID检查二级缓存，如果有数据，则直接使用，否则
                //以各个ID为条件分别发起查询
                query2.list();
                tx2.commit();
                session2.close();
      ```

      ​


2.2.2、 并发访问策略

| 属性                           | 说明                                       | 备注   |
| ---------------------------- | ---------------------------------------- | ---- |
| transactional（事务型）           | 仅在受管理的环境中适用提供Repeatable Read事务隔离级别适用经常被读，很少修改的数据可以防止脏读和不可重复读的并发问题缓存支持事务，发生异常的时候，缓存也能够回滚 | 不推荐  |
| read-write（读写型）              | 提供Read Committed事务隔离级别在非集群的环境中适用适用经常被读，很少修改的数据可以防止脏读更新缓存的时候会锁定缓存中的数据 | 不推荐  |
| nonstrict-read-write（非严格读写型） | 适用极少被修改，偶尔允许脏读的数据（两个事务同时修改数据的情况很少见）不保证缓存和数据库中数据的一致性为缓存数据设置很短的过期时间，从而尽量避免脏读不锁定缓存中的数据 | 不推荐  |
| read-only（只读型）               | 适用从来不会被修改的数据（如参考数据）在此模式下，如果对数据进行更新操作，会有异常事务隔离级别低，并发性能高在集群环境中也能完美运作 | 常用   |

## 优化机制

```
1.使用双向一对多关联，不使用单向一对多
2.灵活使用单向一对多关联
3.不用一对一，用多对一取代
4.配置对象缓存，不使用集合缓存
5.多对多集合使用Set
6.继承类使用显式多态
7.表字段要少，表关联不要怕多，有二级缓存撑腰
```

## 乱码问题

	向mysql插入中文乱码问题
		1、首先需要修改MySQL数据库的配置文件my.ini，此文件放在mysql根目录下。在此文件下查找default-character-set属性，并将其值更改为utf8（注意：不是utf-8，也要注意大小写），这里需要将default-character-set属性全部属性的值修改为utf8。示例：
			default-character-set = utf8
			提示：default-character-set属性有两个，一个在[mysql]下面，另外一个在[mysqld]下面。
		2、同时创建hibernate数据库时需要显示设置数据库的编码方式为utf8。示例：create database daycode default charset=utf8;
		3、做完这两步还是不行，需要修改hibernate的配置文件hibernate.cfg.xml，
在配置文件配置hibernate.connection.url属性。示例：
	<property name="hibernate.connection.url">
		<jdbc:mysql://localhost:3306/hibernate?useUnicode=true&characterEncoding=utf8>
	</property>
	注意：此字符串不能写为jdbc:mysql://localhost:3306/hibernate?useUnicode=true&characterEncoding=utf8，不然会出现编译错误，错误提示为将&连接符改为jdbc:mysql://localhost:3306/hibernate?useUnicode=true&characterEncoding=UTF-8
## Hibernate 中的事务与并发

重要概念

	简介
		事务就是逻辑上的一组操作，组成事务的各个执行单元，操作要么全都成功，要么全都失败
	事务的特性
		原子性 -- 事务不可分割.
		一致性 -- 事务执行的前后数据的完整性保持一致.
		隔离性 -- 一个事务执行的过程中,不应该受到其他的事务的干扰.
		持久性 -- 事务一旦提交,数据就永久保持到数据库中
	如果不考虑隔离性:引发一些读的问题
		脏读 -- 一个事务读到了另一个事务未提交的数据（数据库隔离中最重要的问题）
		不可重复读 -- 一个事务读到了另一个事务已经提交的 update 数据,导致多次查询结果不一致.
		虚读 -- 一个事务读到了另一个事务已经提交的 insert 数据,导致多次查询结构不一致
	通过设置数据库的隔离级别来解决上述读的问题
		未提交读:以上的读的问题都有可能发生.
		已提交读:避免脏读,但是不可重复读，虚读都有可能发生.
		可重复读:避免脏读，不可重复读.但是虚读是有可能发生.
		串行化:以上读的情况都可以避免
	如果想在Hibernate的框架中来设置隔离级别，需要在 hibernate.cfg.xml 的配置文件中通过标签来配置
		hibernate.connection.isolation = 4 来配置
		取值
			1—Read uncommitted isolation
			2—Read committed isolation
			4—Repeatable read isolation
			8—Serializable isolation
丢失更新的问题
	问题
		如果不考虑隔离性，也会产生写入数据的问题，这一类的问题叫丢失更新的问题。
		例如：两个事务同时对某一条记录做修改，就会引发丢失更新的问题
		A 事务和 B 事务同时获取到一条数据，同时再做修改
		如果 A 事务修改完成后，提交了事务
		B 事务修改完成后，不管是提交还是回滚，如果不做处理，都会对数据产生影响
	解决方案有两种
		采用的是数据库提供的一种锁机制，如果采用做了这种机制，在SQL语句的后面添加 for update 子句
		
		当A事务在操作该条记录时，会把该条记录锁起来，其他事务是不能操作这条记录的。
		
		只有当A事务提交后，锁释放了，其他事务才能操作该条记录
		乐观锁
			使用的不是数据库锁机制，而是采用版本号的机制来解决的。给表结构添加一个字段 version=0，默认值是0
			当 A 事务在操作完该条记录，提交事务时，会先检查版本号，如果发生版本号的值相同时，才可以提交事务。同时会更新版本号 version=1.
			当 B 事务操作完该条记录时，提交事务时，会先检查版本号，如果发现版本不同时，程序会出现错误
		悲观锁
			采用的是数据库提供的一种锁机制，如果采用做了这种机制，在SQL语句的后面添加 for update 子句
			当A事务在操作该条记录时，会把该条记录锁起来，其他事务是不能操作这条记录的。
			只有当A事务提交后，锁释放了，其他事务才能操作该条记录
		使用 Hibernate 框架解决丢失更新的问题
			悲观锁（效率较低，不常见）
				使用 session.get(Customer.class, 1,LockMode.UPGRADE); 方法
			乐观锁
				在对应的 JavaBean 中添加一个属性，名称可以是任意的。例如：private Integer version; 提供 get 和 set 方法
				在属性上面添加@Version注解




