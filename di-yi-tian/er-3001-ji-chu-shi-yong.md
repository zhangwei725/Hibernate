# 基础使用

## 一、什么是Hibernate

> ​	Hibernate是一个开放源代码的对象关系映射框架，它对JDBC进行了非常轻量级的对象封装，它将POJO与数据库表建立映射关系，是一个全自动的orm框架，hibernate可以自动生成SQL语句，自动执行，使得Java程序员可以随心所欲的使用对象编程思维来操纵数据库。 Hibernate可以应用在任何使用JDBC的场合，既可以在Java的客户端程序使用，也可以在Servlet/JSP的Web应用中使用，最具革命意义的是，Hibernate可以在应用EJB的J2EE架构中取代CMP，完成数据持久化的重任

## 二、准备工作

### 1、Hibernate包下载地址

1. [官方下载地址](http://hibernate.org/orm/)

### 2、数据库驱动

### 3、导入jar包

#### 3.1、核心jar介绍

1. hibernate.jar

   ```
   hibernate核心包
   ```

2. commons-collections.jar

   ```
   集合包，封装常用集合操作
   ```

3. javassist

   ```
   动态编译及创建字节码包
   ```

4. dom4j

   ```
   解析xml文件包
   ```

5. jta.jar

   ```
   事务处理包
   ```

6. slf4j-api

   ```
   日志包
   ```

7. antlr.jar

   ```
   识别并解析hql语言
   ```

8. hibernate-annotations

   ```
   注解配置
   ```

9. **hibernate-c3p0**：

   ```
   C3P0是一个开放源代码的JDBC连接池，Hibernate的发行包中默认使用此连接池。 
   ```

#### 3.2、pom配置

```xml
   <dependency>
      <groupId>org.hibernate</groupId>
      <artifactId>hibernate-core</artifactId>
      <version>5.2.10.Final</version>
   </dependency>
   <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.0.40</version>
   </dependency>
```

### 4、编写持久化实体类

1. 作用

   > 描述数据库表的结构，表中的字段在类中被描述成属性，将来就可以实现把表中的记录映射成为该类的对象了

2. 编写规则

   1. 提供一个无参数 public访问控制符的构造器 —— 底层需要进行反射

   2. 提供一个标识属性，映射数据表主键字段

   3. 主键分类

      1. 自然主键

         ```
         该主键是对象本身的一个属性。例如：创建一个用户表，每个人都有一个身份证号(唯一的)。使用身份证号作为表的主键，称为自然主键。
         ```

      2. 代理主键

         ```
         该主键不是对象本身的一个属性。例如：创建一个人员表，为每个人员单独创建一个字段，用这个字段作为主键，称为代理主键。
         简单的说:创建表时，新增一个毫无关系的字段，用该字段作为主键
         ```

   4. 主键的生成策略

      1. uuid(重点)
         适用于 char,varchar 类型的作为主键。

         ```
         使用随机的字符串作为主键.
         ```

         ```
         特点：uuid长度大，占用空间大，跨数据库，不用访问数据库就生成主键值，所以效率高且能保证唯一性，移植非常方便，推荐使用。
         ```

      2. native(重点)

         本地策略。根据底层的数据库不同,自动选择适用于该种数据库的生成策略(short,int,long)

         ```
         如果底层使用的 MySQL 数据库：相当于 identity.
         如果底层使用 Oracle 数据库：相当于 sequence.
         特点：根据数据库自动选择，项目中如果用到多个数据库时，可以使用这种方式，使用时需要设置表的自增字段或建立序列，建立表等。
         ```

      3. identity

         适用于 short,int,long 作为主键。但是必须使用在有自动增长机制的数据库中，并且该数据库采用的是数据库底层的自动增长机制。

         ```
         底层使用的是数据库的自动增长(auto_increment)，像 Oracle 数据库没有自动增长机制，而MySql、DB2 等数据库有自动增长的机制。
         ```

      4. sequence

         适用于 short,int,long 作为主键，底层使用的是序列的增长方式。

         ```
         Oracle 数据库底层没有自动增长,若想自动增长需要使用序列。
         ```

      5. assigned

         ```
         主键的生成不用 Hibernate 管理了，必须手动设置主键。
         ```

      6. increment

         ```
         适用于 short,int,long 作为主键，没有使用数据库的自动增长机制
         Hibernate 中提供的一种增长机制，具体步骤：
         先进行查询：select max(id) from user;
         再进行插入：获得最大值+1作为新的记录的主键。
         问题：不能在集群环境下或者有并发访问的情况下使用。
         分析：在查询时，有可能有两个用户几乎同时得到相同的 id，再插入时就有可能主键冲突！
         ```

3. 所有属性提供 public 访问控制符的 set 和 get 方法

4. 标识属性应尽量使用基本数据类型的包装类型（默认值为 null）

### 5、创建.cfg.xml配置文件

#### 5.1、作用

> 它是指定与数据库连接时需要的连接信息，比如连接哪种数据库、登录数据库的用户名、登录密码以及连接字符串等。当然还可以把映射类的地址映射信息放在这里

#### 5.2、常用参数

```xml
<!DOCTYPE hibernate-configuration PUBLIC
    "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!-- 必须配置的属性 -->
        <!-- 连接数据库信息 -->
        <property name="connection.driver_class">com.mysql.jdbc.Driver</property>
        <property name="connection.url">jdbc:mysql:///hibernate?useUnicode=true&amp;characterEncoding=UTF-8</property>
        <property name="connection.username">root</property>
        <property name="connection.password">admin</property>
        <!-- 方言属性：这个是实现跨数据库关键类 具体查询api-->
        <property name="dialect">org.hibernate.dialect.MySQL55Dialect</property>
        <!-- 可选配置属性 -->
        <!-- 是否自动生成表：可选用create update  create-drop validate-->
        <property name="hbm2ddl.auto">update</property>
        <!-- 是否显示sql -->
        <property name="show_sql">true</property>
        <!-- 是否格式化sql -->
        <!-- <property name="format_sql">true</property> -->
        <!-- 以文件路径的方式加载映射文件 -->
        <mapping resource="hbm/User.hbm.xml" />
    </session-factory>
</hibernate-configuration>
```

c3po配置

```xml
<property name="hibernate.connection.provider_class">org.hibernate.service.jdbc.connections.internal.C3P0ConnectionProvider</property>
        <!-- 最小连接数 -->
        <property name="hibernate.c3p0.min_size">5</property>
        <!-- 最大连接数 -->
        <property name="hibernate.c3p0.max_size">20</property>
        <!-- 获得连接的超时时间,如果超过这个时间,会抛出异常，单位毫秒 -->
        <property name="hibernate.c3p0.timeout">120</property>
        <!--JDBC的标准参数，用以控制数据源内加载的PreparedStatements数量。但由于预缓存的statements
            属于单个connection而不是整个连接池。所以设置这个参数需要考虑到多方面的因素。
            如果maxStatements与maxStatementsPerConnection均为0，则缓存被关闭。Default: 0-->
        <property name="hibernate.c3p0.max_statements">100</property>
        <!--maxStatementsPerConnection定义了连接池内单个连接所拥有的最大缓存statements数。Default: 0 -->
        <property name="hibernate.c3p0.maxStatementsPerConnection">100</property>
        <!-- 每隔120秒检查连接池里的空闲连接 ，单位是秒-->
        <property name="hibernate.c3p0.idle_test_period">120</property>
        <!-- 当连接池耗尽，且未达到最大连接数时，一次获取的连接数 -->
        <property name="hibernate.c3p0.acquire_increment">2</property>
        <property name="hibernate.c3p0.testConnectionOnCheckout">true</property>
        <!--最大空闲时间,25000秒内未使用则连接被丢弃。若为0则永不丢弃。Default: 0 -->
        <property name="hibernate.c3p0.maxIdleTime">25000</property>
         <!--初始化时获取10个连接，取值应在minPoolSize与maxPoolSize之间。Default: 3 -->
        <property name="c3p0.initialPoolSize">10</property>
        <!--连接关闭时默认将所有未提交的操作回滚。Default: false -->
           <property name="autoCommitOnClose">false</property>
        <!--c3p0将建一张名为c3p0_test的空表，并使用其自带的查询语句进行测试。如果定义了这个参数那么
            属性preferredTestQuery将被忽略。你不能在这张Test表上进行任何操作，它将只供c3p0测试
            使用。Default: null-->
        <property name="automaticTestTable">c3p0_test</property>
        <!--定义在从数据库获取新连接失败后重复尝试的次数。Default: 30 -->
        <property name="acquireRetryAttempts">30</property>
```

#### 5.3、Hibernate 可选配置

##### 1、Hibernate配置项

1. hibernate.dialect 

   ```
   指定方言后，Hibernate 可以根据低层数据库自动产生优化过的SQL。取值为org.hibenate.dialect.Dialect 的继承类。多数情况下，Hibernate可以根据低层JDBC 返回的metadata 来判断。
   Mysql:http://in.relation.to/2017/02/20/mysql-dialect-refactoring/
   ```

2. hibernate.show_sql 

   ```
   打印所有的SQL 语句到控制台，可以通过设置org.hibernate.SQL 类的日志策略到DEBUG 级，实现同样的效果。取值true|false。
   ```

3. hibernate.format_sql 

   ```
   格式化SQL 语句在打印到控制台或写入日志文件时。取值true|false。
   ```

4. hibernate.default_schema

   ```
   在产生SQL 语句时，在表名前加上映射文件给出的表空间（tablespace）或数据库模式（schema）。取值SCHEMA_NAME。
   ```

5. hibernate.default_catalog 

   ```
   在产生SQL 语句时，在表名前加上映射文件给出的catalog。取值CATALOG_NAME。
   ```

6. hibernate.session_factory_name Hibernate 

   ```
   创建org.hibernate.SessionFactory实例后后会自动的将这个绑定到JNDI 中的名字上。取值jndi/sf。
   hibernate.max_fetch_depth 设置对单个表的外连接数最大深度。0是屏蔽默认的外连接设置。推荐设置为0到3之间。
   ```

7. hibernate.default_batch_fetch_size 

   ```
   设置Hibernate 批量联合查询的尺度。强烈建议。推荐设置为4、8、16。
   ```

8. hibernate.default_entity_mode

   ```
    默认的实体表现模式，通过SessionFactory打开的所有的Session。取值，dynamic-map、dom4j、pojo。
   ```

9. hibernate.order_updates

   ```
    强迫Hibernate 通过被更新项的主键值排序SQL更新。这样可以在高并发时，减少事务死锁。取值true|false。
   ```

10. hibernate.generate_statistics

    > 如果设置为true，Hibernate 将为性能调整，收集统计信息。取值true|false。


1. hibernate.generate_statistics

   ```
   如果设置为true，Hibernate 将为性能调整，收集统计信息。取值true|false。
   ```

2. hibernate.use_identifier_rollback

   ```
    如果设置为true，产生的标识属性将被重置成默认值，当对象被删除后。取值为true|false。
   ```

3. hibernate.use_sql_comments 

   ```
   如果设置为true，Hiberante 将为SQL 产生注释，这样更利于调试。默认值为 false。取值为true|false。
   ```

##### 2、 JDBC 配置

1. hibernate.jdbc.fetch_size 

   ```
   指定JDBC 的查询尺度。通过调用（Statement.setFetchSize()）。
   ```

2. hibernate.jdbc.batch_size

   ```
    指定Hibernate 如何使用JDBC2批量更新。取值，推荐5到30。
   ```

3. hibernate.jdbc.batch_versioned_data 

   ```
   设置这个属性为true，JDBC 将返回executeBatch 执行后正确的行数。打开这个参数，通常是安全的。Hibernate 将自动的译码这些数据使用批量DML。默认为false。取值true|false。
   ```

4. hibernate.jdbc.factory_class

   ```
    选择一个定制的org.hibernate.jdbc.Batcher。所有的应用程序不需要配置这个属性。取值，定义工厂的类名。
   ```

5. hibernate.jdbc.use_scrollable_resultset Hibernate 

   ```
   使用JDBC2的可滚动记录集。当使用用户提供的JDBC 连接时，需要设置这个参数。否则，Hibernate 使用连接MetaData。取值true|false。
   ```

6. hibernate.jdbc.use_streams_for_binary

   ```
   当读或写二进制数据或序列化数据从JDBC 或到JDBC，使用流。系统级的数据。设置true|false。
   ```

7. hibernate.jdbc.use_get_generated_keys

   ```
   在插入后，可以使用JDBC3的PreparedStatement.getGeneratedKeys()中的值找回本地产生的键值。要求JDBC3+和JRE1.4+，如果使用Hibernate identifier generator 后你的驱动程序有问题，请设置为false。默认情况下，设法连接MetaData 来决定。取值，true|false。
   ```

8. hibernate.connection.provider_class

   ```
    实现了：org.hibernate.connection.ConnectionProvider 接口的类的名称，为Hibernate 提供连接。
   ```

9. hibernate.connection.isolation 

   ```
   设置JDBC 事务隔离的级别。检查java.sql.Connection 的定义的常量值，但要注意大多数数据库不支持所有的隔离级别、一些附加的和非标准的隔离级别。取值，1、2、4、8。
   ```

10. hibernate.connection.autocommit JDBC

    ```
    共享连接的自动提交。（不推荐）取值，true|false。
    ```


1. hibernate.connection.release_mode 

   ```
   指定什么时候，Hibernate 应该释放JDBC 连接。
   默认情况下，JDBC 是一直存在，只到Session 是被明确关闭或断开连接时。对于应用的服务器JTA 数据源，你应该使用after_statement 强制释放JDBC连接在每个JDBC 请求结束后。
   对于非JTA 数据源，通常是在每个事务结束后释放JDBC 连接是有意义的。将该值设为auto 时，JTA 和CMT 事务策略时，是选择after_statement 方式。JDBC 事务策略时，是选择after_transaction。取值，auto(default)，on_close，after_statment，after_transaction。注意：这个设置只影响通过 SessionFactory.openSession 打开的session。对于通过SessionFactory.getCurrentSession 获取的session，CurrentSessionContext 实现类的配置是用来控制这些session 的连接释放模式。
   ```

2. hibernate.connection.

   ```
   传递这些属性到DriverManager.getConnection中。
   ```

3. hibernate.jndi.

   ```
   传递这些属性到JNDI InitialContextFactory。
   ```

##### 3、Cache 属性

1. hibernate.cache.provider_class 

   ```
   定制的CacheProvider 的类名。
   ```

2. hibernate.cache.use_minimal_puts 

   ```
   花费更多的读操作，来优化二级缓存的最少写操作。这个操作对于集群缓存是非常有用的。在Hibernate3中，对于集群缓存是默认开启该功能的。取值，true|false。
   ```

3. hibernate.cache.use_query_cache 

   ```
   开启查询缓存，个别查询肯定应该开启查询缓存。取值，true|false
   ```

### 6、编写映射文件

#### 6.1、作用

​	它是指定数据库表和映射类之间的关系，包括映射类和数据库表的对应关系、表字段和类属性类型的对应关系以及表字段和类属性名称的对应关系等

#### 6.2、实体映射

1. ##### 简介

   ```
   在Hibernate中的对象关系映射就是把实体类与数据库中的表相对应，实现实体类中的属性与数据库表中的字段一一对应映射是按照持久化类（实体类）的定义一创建的，而非表的定义。配置文件可以手动写，也可以使用工具生成 如：MyEclipse或者idea生成实体类时，可以自动生成配置文件
   ```

2. ##### 配置文件名字：类名.hbm.xml

   ```
   <?xmlversion="1.0"encoding='UTF-8'?>  
     
   <!DOCTYPE hibernate-mapping PUBLIC   
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"   
        "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">  
   <hibernate-mappingpackage="包名">  
      <classname="类名"table="表名">  
         <idname="主键在java类中的字段名"column="对应表中字段"type="类型 ">  
             <generatorclass="主键生成策略"/>  
         </id>  
     
             ……   
       </class>  
   </hibernate-mapping>
   ```

3. ##### 映射文件结构详解

   1. 说明

      ```
      Hibernate的持久化类和关系数据库之间的映射通常是用一个XML文档来定义的。该文档通过一系列XML元素的配置，来将持久化类与数据库表之间建立起一一映射。这意味着映射文档是按照持久化类的定义来创建的，而不是表的定义
      ```

   2. 声明文档格式

      ```
      所有的xml映射都需要DOCTYPE
      <!DOCTYPE hibernate-mapping PUBLIC
          "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
          "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">
      ```

   3. 映射结构

      1. <hibernate-mapping>(根元素) 每一个hbm.xml文件都有唯一的一个根元素，包含一些可选的属性

      2. 可选属性

         ```
         <hibernate-mapping
                  schema="schemaName"                          (1)
                  catalog="catalogName"                        (2)
                  default-cascade="cascade_style"              (3)
                  default-access="field|property|ClassName"    (4)
                  default-lazy="true|false"                    (5)
                  auto-import="true|false"                     (6)
                  package="package.name"                       (7)
          />
         1、package
         	指定一个包前缀，如果在映射文档中没有指定全限定的类名，就使用这个作为包名
         	<hibernate-mapping package="com.xxx.xxx.bean">
         		<class name="User" ...>
         	</hibernate-mapping>

         2、schema：数据库schema的名称
         3、catalog：数据库catalog的名称
         	一个数据库系统包含多个Catalog，每个Catalog包含多个Schema，每个Schema包含多个数据库对象（表、视图、字段等）如数据库对象表的全限定名可表示为：Catalog名.Schema名.表名
         4、default-cascade：默认的级联风格,默认为none
         	级联操作也就是在操作一端的数据如果影响到多端数据时会进行级联操作，一对一的时候直接写在标签
         	1、none 就是不使用级联操作，默认级联是none。
         	2、save-update 也就是只有对象保存操作（持久化操作）或者是持久化对象的更新操作，才会级联操作关联对象（子对象）。
         	3、all 对持久化对象的所有操作都会级联操作关联对象（子对象）。
         	4、all-delete-orphan，在多端进行删除操作时，会再多端表中留下null空纪录，设置了级联操作为delete之会将表中表示关联的外键id置成null，不会将这条纪录也删除掉，而把级联设置成delete-orphan就不会留有空纪录，而是级联的把相关纪录删除掉。
         5、default-access：Hibernate用来访问属性的策略
         	property(默认)，即使用get/set 方法访问属性；
         	field，即绕过get/set方法，直接使用反射访问字段属性;
         	ClassName，是指使用实现了org.hibernate.property.PropertyAccessor接口的类的具体策略来访问字段属性
         6、default-lazy：指定了未明确注明lazy属性的Java属性和集合类，Hibernate会采取什么样的默认加载风格,默认为true
         7、auto-import：指定我们是否可以在查询语言中使用非全限定的类名,默认为true,如果项目中有两个同名的持久化类,则最好在这两个类的对应的映射文件中配置为false
         ```

      3. <class>(子元素)

         ```
         <class
                 name="ClassName"                              (1)
                 table="tableName"                             (2)
                 discriminator-value="discriminator_value"     (3)
                 mutable="true|false"                          (4)
                 schema="owner"                                (5)
                 catalog="catalog"                             (6)
                 proxy="ProxyInterface"                        (7)
                 dynamic-update="true|false"                   (8)
                 dynamic-insert="true|false"                   (9)
                 select-before-update="true|false"             (10)
                 polymorphism="implicit|explicit"              (11)
                 where="arbitrary sql where condition"         (12)
                 persister="PersisterClass"                    (13)
                 batch-size="N"                                (14)
                 optimistic-lock="none|version|dirty|all"      (15)
                 lazy="true|false"                             (16)
                 entity-name="EntityName"                      (17)
                 check="arbitrary sql check condition"         (18)
                 rowid="rowid"                                 (19)
                 subselect="SQL expression"                    (20)
                 abstract="true|false"                         (21)
                 entity-name="EntityName"                      (22)
                 node="element-name"                           (23)
         />
         属性说明
         1. name (可选): 持久化类(或者接口)的Java全限定名。 如果这个属性不存在，Hibernate将假定这是一个非POJO的实体映射。
         2. table (可选 - 默认是类的非全限定名): 对应的数据库表名。
         3. discriminator-value (可选 - 默认和类名一样): 一个用于区分不同的子类的值，在多态行为时使用。它可以接受的值包括 null 和 not null。
         4. mutable (可选，默认值为true): 表明该类的实例是可变的或者不可变的。
         5. schema (可选): 覆盖在根＜hibernate-mapping＞元素中指定的schema名字。
         6. catalog (可选): 覆盖在根＜hibernate-mapping＞元素中指定的catalog名字。
         7. proxy (可选): 指定一个接口，在延迟装载时作为代理使用。 可以在这里使用该类自己的名字。
         8. dynamic-update (可选, 默认为 false): 指定用于UPDATE 的SQL将会在运行时动态生成，并且只更新那些改变过的字段。
         9. dynamic-insert (可选, 默认为 false): 指定用于INSERT的 SQL 将会在运行时动态生成，并且只包含那些非空值字段。
         10. select-before-update (可选, 默认为 false): 指定Hibernate除非确定对象真正被修改了(如果该值为true－译注)，否则不会执行SQL UPDATE操作。在特定场合(实际上，它只在一个瞬时对象(transient object)关联到一个 新的session中时执行的update()中生效)，这说明Hibernate会在UPDATE 之前执行一次额外的SQL SELECT操作，来决定是否应该执行 UPDATE。
         11. polymorphism(多态) (可选, 默认值为 implicit (隐式) ): 界定是隐式还是显式的使用多态查询(这只在Hibernate的具体表继承策略中用到)。
         12. where (可选) 指定一个附加的SQLWHERE 条件， 在抓取这个类的对象时会一直增加这个条件。
         13. persister (可选): 指定一个定制的ClassPersister。
         14. batch-size (可选,默认是1) 指定一个用于 根据标识符(identifier)抓取实例时使用的"batch size"(批次抓取数量)。
         15. optimistic-lock(乐观锁定) (可选，默认是version): 决定乐观锁定的策略。
         16. lazy (可选): 通过设置lazy="false"， 所有的延迟加载(Lazy fetching)功能将被全部禁用(disabled)。
         17. entity-name (可选，默认为类名): Hibernate3允许一个类进行多次映射( 前提是映射到不同的表)，并且允许使用Maps或XML代替Java层次的实体映射 (也就是实现动态领域模型，不用写持久化类－译注)。
         18. check (可选): 这是一个SQL表达式， 用于为自动生成的schema添加多行(multi-row)约束检查。
         19. rowid (可选): Hibernate可以使用数据库支持的所谓的ROWIDs，例如： Oracle数据库，如果设置这个可选的rowid， Hibernate可以使用额外的字段rowid实现快速更新。ROWID是这个功能实现的重点， 它代表了一个存储元组(tuple)的物理位置。
         20. subselect (可选): 它将一个不可变(immutable)并且只读的实体映射到一个数据库的 子查询中。当想用视图代替一张基本表的时候，这是有用的，但最好不要这样做。更多的介绍请看下面内容。
         21. abstract (可选): 用于在＜union-subclass＞的继承结构 (hierarchies)中标识抽象超类
         ```

   4. 映射主键

      1. ID

         1. 通常情况下，hibernate建议为持久化类定义一个标识属性，用于唯一的标识某个持久化实例,

            标识属性通过<id.../>元素来指定，<id.../>元素中的name属性的值就是持久化类的标识属性名

         2. 可选属性

            ```
            <id
                    name="propertyName"                                          (1)
                    type="typename"                                              (2)
                    column="column_name"                                         (3)
                    unsaved-value="null|any|none|undefined|id_value"             (4)
                    access="field|property|ClassName"                            (5)
                    node="element-name|@attribute-name|element/@attribute|.">
                    <generator class="generatorClass"/>
            </id>
            ```

         3. 属性说明

            ```
            1、name (可选): 标识属性的名字。
            2、type (可选): 标识Hibernate类型的名字。
            3、column (可选 - 默认为属性名): 主键字段的名字。
            4、unsaved-value (可选 - 默认为一个字段判断（sensible）的值): 一个特定的标识属性值，用来标志该实例是刚刚创建的，尚未保存。 这可以把这种实例和从以前的session中装载过（可能又做过修改--译者注） 但未再次持久化的实例区分开来。
            5、access (可选 - 默认为property): Hibernate用来访问属性值的策略。
            如果 name属性不存在，会认为这个类没有标识属性。unsaved-value 属性很重要！如果你的类的标识属性不是默认为 正常的Java默认值（null或零），你应该指定正确的默认值。还有一个另外的<composite-id>定义可以访问旧式的多主键数据。 我们强烈不建议使用这种方式
            ```

         4. 注意事项

            ```
            尽量避免使用复杂的物理主键，应该为数据库增加一列，作为逻辑主键，hibernate为这种逻辑主键提供了主键生成器，用<generator>元素来指定主键的生成器=该元素的作用是指定主键的生成器，通过一个class属性指定生成器对应的类。(通常与<id>元素结合使用)

            	<id name="id" column="ID" type="integer">
            		<generator class="native" />
            		--native是Hibernate主键生成器的实现算法之一，
            		由Hibernate根据底层数据库自行判断采用identity、hilo、sequence其中一种作为主键生成方式。
            	</id>
            	
            	Hibernate提供的内置生成器
            	1、assigned算法
            	2、hilo算法
            	3、seqhilo算法
            	4、increment算法
            	5、identity算法
            	6、sequence算法
            	7、native算法
            	8、uuid.hex算法
            	9、uuid.string算法
            	10、foregin算法
            	11、select算法
            ```

         5. Generator

            1. 说明

               ```
               可选的<generator>子元素是一个Java类的名字， 用来为该持久化类的实例生成唯一的标识。如果这个生成器实例需要某些配置值或者初始化参数， 用<param>元素来传递
               ```

            2. 可选属性

               ```
                 <generator class="org.hibernate.id.TableHiLoGenerator">
                               <param name="table">uid_table</param>
                               <param name="column">next_hi_value_column</param>
                </generator>
                
               所有的生成器都实现net.sf.hibernate.id.IdentifierGenerator接口。 这是一个非常简单的接口；某些应用程序可以选择提供他们自己特定的实现。当然， Hibernate提供了很多内置的实现。下面是一些内置生成器的快捷名字
               ```

            3. 常见主键生成方式

               1. increment

                  ```
                  主键递增生成，其生成方式与底层数据库无关，大部分数据库都支持，该方式的实现机制是在当前应用实例中维持一个变量，以保存着当前的最大值，之后每次需要生成主键的时候将此值加1作为转。其不足之处是当多个线程并发对数据库表进行写操作时，可能出现相同的主键值，发生主键重复的冲突，所以在多线程并发操作的时候不应该使用该方法。
                  <generator class="increment" />
                  ```

               2. identity

                  ```
                  与底层数据库有关，要求数据库支持Identity，如MySQL中auto_increment，SQL Server中的identity。支持的数据库有MySQL,SQL Server,DB2,Sybase,但是不支持oracle.支持并发
                  <generator class="identity" />
                  ```

               3. assigned

                  ```
                  主键由外部程序负责生成，无需Hibernate参与。----如果要由程序代码来指定主键,就采有这种
                  <generator class="assigned" />
                  ```

                  ​

               4. sequence

                  ```
                  使用序列生成主键，需要底层数据库支持
                  <generator class="sequence">
                      <param name="sequence">seq_name</param>
                  </generator>
                  ```

   5. 映射普通属性

      1. <property>

         1. 说明

            ```
            为类定义了一个持久化的,JavaBean风格的属性
            ```

         2. 可选属性

            ```
            <property
                    name="propertyName"                                          (1)
                    column="column_name"                                        (2)
                    type="typename"                                              (3)
                    update="true|false"                                          (4)
                    insert="true|false"                                          (4)
                    formula="arbitrary SQL expression"                          (5)
                    access="field|property|ClassName"                            (6)
                    lazy="true|false"                                            (7)
                    unique="true|false"                                          (8)
                    not-null="true|false"                                        (9)
                    optimistic-lock="true|false"                                (10)
                    node="element-name|@attribute-name|element/@attribute|."
            />
            ```

         3. 属性说明

            ```
            1、name: 属性的名字,以小写字母开头。
            2、column (可选 - 默认为属性名字): 对应的数据库字段名。 也可以通过嵌套的<column>元素指定。
            3、type (可选): 一个Hibernate类型的名字。
            4、update, insert (可选 - 默认为 true) : 表明用于UPDATE 和/或 INSERT 的SQL语句中是否包含这个被映射了的字段。这二者如果都设置为false 则表明这是一个“外源性（derived）”的属性，它的值来源于映射到同一个（或多个） 字段的某些其他属性，或者通过一个trigger(触发器）或其他程序。
            5、formula (可选): 一个SQL表达式，定义了这个计算 （computed） 属性的值。计算属性没有和它对应的数据库字段。
            6、access (可选 - 默认值为 property): Hibernate用来访问属性值的策略。
            7、lazy (可选 - 默认为 false): 指定 指定实例变量第一次被访问时，这个属性是否延迟抓取（fetched lazily）（ 需要运行时字节码增强）。
            8、unique (可选): 使用DDL为该字段添加唯一的约束。 此外，这也可以用作property-ref的目标属性。
            9、not-null (可选): 使用DDL为该字段添加可否为空（nullability）的约束。
            10、optimistic-lock (可选 - 默认为 true): 指定这个属性在做更新时是否需要获得乐观锁定（optimistic lock）。 换句话说，它决定这个属性发生脏数据时版本（version）的值是否增长。
            ```

### 7、配置hbm.xml

​	类名.hbm.xml

1. java类

   ```
   public class User implements Serializable {
       private Integer id;
       private String userName;
       private String email;
       ...
   }
   ```

2. hbm映射文件

   ```
   <?xml version='1.0' encoding='utf-8'?>
   <!DOCTYPE hibernate-mapping PUBLIC
           "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
           "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
   <hibernate-mapping>

       <class name="com.werner.demo.bean.User" table="TB_USER" schema="hibernate">
           <id name="id">
               <column name="ID" sql-type="varchar(32)"/>
               <generator class="uuid"/>
           </id>
           <property name="blogUrl">
               <column name="BLOG_URL" sql-type="varchar(64)" length="64" not-null="true"/>
           </property>
           <property name="email">
               <column name="EMAIL" sql-type="varchar(64)" length="64" not-null="true"/>
           </property>
           <property name="userName">
               <column name="USER_NAME" sql-type="varchar(20)" length="20" not-null="true"/>
           </property>
       </class>
     
   </hibernate-mapping>
   ```

## 三、hibernate操作流程

### 1、步骤

1. 读取并解析配置文件 

   ```
    Configuration cfg = new Configuration().configure();
   ```

2. 创建SessionFactory  

   ```
    SessionFactory  factory = cfg.buildSessionFactory();
    注意4.x的版本需要ServiceRegistry
       SessionFactory sessionFactory = configiguration
       .buildSessionFactory(new StandardServiceRegistryBuilder().applySettings(configiguration.getProperties()).build()); 
   ```

3. 打开Session 

   ```
   Session session = factory.openSession();
   ```

4. 创建事务Transaction 

   ```
   Transaction transaction = session.beginTransaction();
   ```

5. 持久化操作 

   ```
   User user = session.get(User.class, 1L);
   ```

6. 提交事务 

   ```
   transaction.commit();
   ```

7. 关闭Session 

   ```
   session.close();
   ```

8. 关闭SessionFactory 

   ```
   factory.close();
   ```

### 2、示例图

![](http://opzv089nq.bkt.clouddn.com/17-7-7/28249302.jpg)



## 四、其它

### 1、以下是各数据库对应的方言（Dialect）：

| 数据库                       | 方言（Dialect）                              |
| ------------------------- | ---------------------------------------- |
| DB2                       | org.hibernate.dialect.DB2Dialect         |
| DB2 AS/400                | org.hibernate.dialect.DB2400Dialect      |
| DB2 OS390                 | org.hibernate.dialect.DB2390Dialect      |
| PostgreSQL                | org.hibernate.dialect.PostgreSQLDialect  |
| MySQL5                    | org.hibernate.dialect.MySQL5Dialect      |
| MySQL5 with InnoDB        | org.hibernate.dialect.MySQL5InnoDBDialect |
| MySQL with MyISAM         | org.hibernate.dialect.MySQLMyISAMDialect |
| Oracle（any version）       | org.hibernate.dialect.OracleDialect      |
| Oracle 9i                 | org.hibernate.dialect.Oracle9iDialect    |
| Oracle 10g                | org.hibernate.dialect.Oracle10gDialect   |
| Oracle 11g                | org.hibernate.dialect.Oracle10gDialect   |
| Sybase                    | org.hibernate.dialect.SybaseASE15Dialect |
| Sybase Anywhere           | org.hibernate.dialect.SybaseAnywhereDialect |
| Microsoft SQL Server 2000 | org.hibernate.dialect.SQLServerDialect   |
| Microsoft SQL Server 2005 | org.hibernate.dialect.SQLServer2005Dialect |
| Microsoft SQL Server 2008 | org.hibernate.dialect.SQLServer2008Dialect |
| SAP DB                    | org.hibernate.dialect.SAPDBDialect       |
| Informix                  | org.hibernate.dialect.InformixDialect    |
| HypersonicSQL             | org.hibernate.dialect.HSQLDialect        |
| H2 Database               | org.hibernate.dialect.H2Dialect          |
| Ingres                    | org.hibernate.dialect.IngresDialect      |
| Progress                  | org.hibernate.dialect.ProgressDialect    |
| Mckoi SQL                 | org.hibernate.dialect.MckoiDialect       |
| Interbase                 | org.hibernate.dialect.InterbaseDialect   |
| Pointbase                 | org.hibernate.dialect.PointbaseDialect   |
| FrontBase                 | org.hibernate.dialect.FrontbaseDialect   |
| Firebird                  | org.hibernate.dialect.FirebirdDialect    |

### 2、 Hibernate主键生成策略总结

| 标识符生成器      | 描述                                       |
| :---------- | :--------------------------------------- |
| increment   | 适用于代理主键。由Hibernate自动以递增方式生成。             |
| identity    | 适用于代理主键。由底层数据库生成标识符。                     |
| sequence    | 适用于代理主键。Hibernate根据底层数据库的序列生成标识符，这要求底层数据库支持序列。 |
| hilo        | 适用于代理主键。Hibernate分局high/low算法生成标识符。      |
| seqhilo     | 适用于代理主键。使用一个高/低位算法来高效的生成long，short或者int类型的标识符。 |
| native      | 适用于代理主键。根据底层数据库对自动生成标识符的方式，自动选择identity、sequence或hilo。 |
| uuid.hex    | 适用于代理主键。Hibernate采用128位的UUID算法生成标识符。     |
| uuid.string | 适用于代理主键。UUID被编码成一个16字符长的字符串。             |
| assigned    | 适用于自然主键。由Java应用程序负责生成标识符。                |
| foreign     | 适用于代理主键。使用另外一个相关联的对象的标识符。                |

### 3、注解生成总结

#### 3.1、Java基本类型的Hibernate映射类型

| Java类型                | Hibernate映射类型 | 标准SQL类型          | 大小和取值范围      |
| --------------------- | ------------- | ---------------- | ------------ |
| int/Integer           | int/integer   | INTEGER          | 4Byte        |
| long/Long             | long          | BIGINT           | 8Byte        |
| short/Short           | short         | SAMLLINT         | 2Byte        |
| byte/Byte             | byte          | TINYINT          | 1Byte        |
| float/Float           | float         | FLOAT            | 4Byte        |
| double/Double         | double        | DOUBLE           | 8Byte        |
| BigDecimal            | big_decimal   | NUMBERIC         | Numeric(8,2) |
| char/Character/String | character     | CHAR(1)          | 定长字符         |
| String                | string        | VARCHAR          | 变长字符         |
| boolean/Boolean       | boolean       | BIT              | 布尔类型         |
| boolean/Boolean       | yes/no        | CHAR(1)('Y'/'N') | 布尔类型         |
| boolean/Boolean       | true/false    | CHAR(1)('T'/'F') | 布尔类型         |

#### 3.2、 Java时间和日期类型的Hibernate映射类型

| Java类型                            | Hibernate映射类型 | 标准SQL类型   | 描述                 |
| --------------------------------- | ------------- | --------- | ------------------ |
| java.util.Date/java.sql.Date      | date          | DATE      | 日期，yyyy-mm-dd      |
| java.util.Date/java.sql.TIme      | time          | TIME      | 时间，hh：mm：ss        |
| java.util.Date/java.sql.Timestamp | timestamp     | TIMESTAMP | 时间戳，yyyymmddhhmmss |
| java.util.Calendar                | calendar      | TIMESTAMP | 同上                 |
| java.util.Calendar                | calendar_date | DATE      | 日期，yyyy-mm-dd      |

\* 当程序类型为java.sql.Timestamp, 数据库中表属性类型为timestamp的情况下，即使用户以空值插入数据，数据库系统仍然会自动填充timestamp的值

#### 3.3、 Java 大对象类型的Hibernate映射类型

| Java类型        | Hibernate映射类型            | 标准SQL类型        | MySql类型 | Oracle类型 |
| ------------- | ------------------------ | -------------- | ------- | -------- |
| byte[]        | binary                   | VARBINARY/BLOB | BLOB    | BLOB     |
| String        | text                     | CLOB           | TEXT    | CLOB     |
| serializable  | 实现serializable接口的一个java类 | VARBINARY/BLOB | BLOB    | BLOB     |
| java.sql.Clob | clob                     | CLOB           | TEXT    | CLOB     |
| java.sql.Blob | blob                     | BLOB           | BLOB    | BLOB     |

### 4、 Java 大对象类型的Hibernate映射类型

PO

```
persistant object持久对象
PO就是数据库中的一条记录
好处是可以把一条记录作为一个对象处理，可以方便的转为其它对象
```

BO

```
business object业务对象
主要作用是把业务逻辑封装为一个对象。这个对象可以包括一个或多个其它的对象。
这样处理业务逻辑时，我们就可以针对BO去处理
```

VO 

```
value object值对象
ViewObject表现层对象
```

DTO 

```
Data Transfer Object数据传输对象

主要用于远程调用等需要大量传输对象的地方。

比如我们一张表有100个字段，那么对应的PO就有100个属性。

但是我们界面上只要显示10个字段，

客户端用WEB 来获取数据，没有必要把整个PO对象传递到客户端，

这时我们就可以用只有这10个属性的DTO来传递结果到客户端，这样也不会暴露服务端表结构.到达客户端以后，如果用这个对象来对应界面显示，那此时它的身份就转为VO

```

POJO 

```
plain ordinary java object 简单java对象

是一个中间对象，也是我们最常打交道的对象。
一个POJO持久化以后就是PO
直接用它传递、传递过程中就是DTO
直接用来对应表示层就是VO

```

DAO：

```
data access object数据访问对象
这个大家最熟悉，和上面几个O区别最大，基本没有互相转化的可能性
主要用来封装对数据库的访问。通过它可以把POJO持久化为PO，用PO组装出来VO、DTO
```

![](http://opzv089nq.bkt.clouddn.com/17-7-11/38689666.jpg)