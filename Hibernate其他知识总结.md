# 其他知识总结

## 1、hibernate对象三种状态

### 1.1、状态说明

1. transient(瞬时态) -实体刚刚被实例化并且没有与持久化上下文关联。也没有写入数据库中。通常还没有 ID 值
2. managed(托管态)或者 persistent(持久态) -实体已经有了 ID 并且与持久化上下文关联。在物理数据库中可能存在也可能不存在
3. detached(游离态) -实体已经有了关联的 ID，但是不再与持久化上下文关联(通常因为持久化上下文已经关闭或者实体被上下文 evicted(回收、清理))
4. removed(删除态) -实体已经有了 ID 并且与持久化上下文有了关联，然而数据库已经计划删除数据。

### 1.2、三种状态的转变



### 1.3、对象状态转换的相关方法描述

1. get、load、find: 方法的使用上较为类似，get和find方法一样。他们都是将数据库中对应Id的数据映射为Java对象，此时对象变为持久化状态。

2. save: 保存，此时Java对象已经与数据库记录建立的关系。将对象从临时状态的变为持久化状态或者将游离状态的数据变为持久状态。

   saveOrUpdate: 保存或者更新，如果没有与数据库记录所对应的oid，则执行保存，如果有，则执行更新。将对象从临时状态的变为持久化状态或者将游离状态的数据变为持久状态。

3. delete: 删除对象，将对象从持久化状态或者游离状态变为临时状态。

   clear: 清空session缓存。将对象从持久状态变为临时状态。

   evict: 清除指定的对象。将对象从持久状态变为临时状态。

## 3、持久态对象特点

3.1、持久态对象，在同一Session中只存在同一个。

1. 如果连接不关闭，多次查询同一条数据，只返回同一个对象，也就是只查询一次数据库。
2. 此功能也被称为一级缓存，但实际开发中实用性很低。

3.2、修改持久态对象的属性，可以自动同步到数据库对应的数据中。

1. 当修改了一个持久态对象的属性，而且提交了事务，则数据库自动调用更新操作，也一起修改。

    例如:当登陆后，要求将当前系统时间，作为最后登陆时间保存到数据库中时，可以使用。

## 2、 hibernate一级缓存

### 2.1、 说明

​	一级缓存的生命周期和session的生命周期一致，当前sessioin一旦关闭，一级缓存就消失，因此一级缓存也叫 session 级的缓存或事务级缓存，一级缓存只存实体对象的 ，它不会缓存一般的对象属性（查询缓存可以），即当获得 对象后，就将该对象的缓存起来，如果在同一session中如果再去获取这个对象 时，它会先判断缓存中有没有该对象的 ID，如果有就直接从缓存中取出，反之则去数据库中取，取的同时将该对象的缓存起来，有以下方法可以 支持一级缓存

1. get()
2. load()
3. iterate（查询实体对象）

二级缓存也称进程级的缓存或SessionFactory级的缓存，二级缓存可以被所有的session共享，二级缓存的生命周期和 SessionFactory的生命周期一致。hibernate为实现二级缓存，只提供二级缓存的接口供第三方来实现,二级缓存也是缓存实体对象 ，其实现原理与一级缓存的差不多吧，其方法与一级的相同，只是缓存的生命周期不一样而已

2.2、 使用

2.2.1、 二级缓存配置

1. 导入jar ehcache

2. 在hibernate.cfg.xml中开启二级缓存

   ```
   <propertyname="hibernate.cache.use_second_level_cache">true</property>
   ```

3. 配置二级缓存技术提供商

   ```
   <propertyname="hibernate.cache.provider_class">org.hibernate.cache.EhCacheProvider</property>
   ```

4. 配置缓存数据对象并发策略

   ```
     <class-cache usage="read-write" class="包名类名"/>  
   <collection-cache usage="read-write" collection="包名.类名.集合属性"/>  
   ```

5. 添加二级缓存配置文件到resource文件夹下

   ```
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
     
   </ehcache>  
   ```

6. 测试代码​​


2.2.2、 并发访问策略

| transactional（事务型）           | 仅在受管理的环境中适用提供Repeatable Read事务隔离级别适用经常被读，很少修改的数据可以防止脏读和不可重复读的并发问题缓存支持事务，发生异常的时候，缓存也能够回滚 |
| ---------------------------- | ---------------------------------------- |
| read-write（读写型）              | 提供Read Committed事务隔离级别在非集群的环境中适用适用经常被读，很少修改的数据可以防止脏读更新缓存的时候会锁定缓存中的数据 |
| nonstrict-read-write（非严格读写型） | 适用极少被修改，偶尔允许脏读的数据（两个事务同时修改数据的情况很少见）不保证缓存和数据库中数据的一致性为缓存数据设置很短的过期时间，从而尽量避免脏读不锁定缓存中的数据 |
| read-only（只读型）               | 适用从来不会被修改的数据（如参考数据）在此模式下，如果对数据进行更新操作，会有异常事务隔离级别低，并发性能高在集群环境中也能完美运作 |







## 3、 hibernate查询缓存

## 4、hibernate锁概念

## 5、hibernate数据加载

1、即时加载（Immediate Loading）
当实体加载完成后，立即加载与实体相关联的数据。即当实体加载完成后，Hibernate自动立即读取与实体相关联的数据，并且填充到实体对应的属性中。这种加载通常有多条select语句，即select实体数据后，同时select实体相关联的数据。

2、延迟加载（Lazy Loading）
实体加载时，其关联数据并不是立即读取，而是当关联数据第一次被访问时再进行读取，这种加载方式在第一次访问关联数据时，必须在同一个session中，否则会报session已关闭错误。
延迟加载通过在实体的hbm文件中的对应属性中设定lazy=”true”实现。Hibernate3默认的加载方式是延迟加载。即默认lazy=”true”，主要用于one-to-many场合。

3、预先加载（Eager Loading）
预先加载时，实体及关联对象同时读取，与即时加载类似，但是预先加载是使用”outer-join”通过一条select语句同时读取。
注意：当实体间关联比较复杂时，比如多层关联，Hibernate生成的”outer join SQL”可能过于复杂，此时可以通过设定全局变量（hibernate.max_fetch_depth）限定join的层次（一般设定为5层）。

4、批量加载（Batch Loading）
对于即时和延迟加载，可以采用批量加载进行优化。
批量加载就是通过批量提交多个限定条件，一次多个限定条件的数据读取。同时在实体映射文件中的class节点，通过配置”batch-size”参数打开批量加载机制，并限定每次批量加载数据的数量,一般来说该值<10较合理.



必须重写Hashcode的情况

1.如果想把持久类的实例放入set中（多值关联时，1对多），建议实现equals和hashcode
2.想重用托管实例时，也要实现equals和hashcode

3.多个字段组合作为联合主键，必须实现equals和hashcode方法