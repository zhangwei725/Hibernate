# Hibernate 映射关系

## 1、  映射文件说明

### 1.1 名词解释

1. 关系：事物之间相互作用、相互联系的状态。
2. 关联：名词：表示对象（数据库表）之间的关系；动词：将对象（数据库表）之间通过某种方式联系起来。
3. 映射：将一种形式转化为另一种形式，包括关系。
4. 级联：有关系的双方中操作一方，另一方也将采取一些动作
5. 值类型：对象不具备数据库同一性，属于一个实体实例其持久化状态被嵌入到所拥有的实体的表行中，没有标识符。
6. 实体类型：具有数据库标识符。

### 1.1 文件功能说明

1. 映射类（.Java）

   ```
   映射类（*.java）：它是描述数据库表的结构，表中的字段在类中被描述成属性，将来就可以实现把表中的记录映射成为该类的对象了
   ```

2. 映射文件（.hbm.xml）

   ```
   映射文件（*.hbm.xml）：它是指定数据库表和映射类之间的关系，包括映射类和数据库表的对应关系、表字段和类属性类型的对应关系以及表字段和类属性名称的对应关系等
   ```

3. 数据库配置文件（.properties/.cfg.xml）

   ```
   数据库配置文件（.properties/.cfg.xml）：它是指定与数据库连接时需要的连接信息，比如连接哪种数据库、登录数据库的用户名、登录密码以及连接字符串等。当然还可以把映射类的地址映射信息放在这里
   ```

## 2、  映射关系

概要

1. 一对一关联分为主键关联与外键关联

   ```
   主键关联：不必加额外的字段，只是主表和辅表的主键相关联，即这两个主键的值是一样的。

   外键关联：辅表有一个额外的字段和主表相关联，或者两个表都有额外的字段与对应表的相关联。
   ```

2. 一对多 

   ```
   多对一关联映射：在多的一端加入一个外键指向一的一端，它维护的关系是多指向一
   一对多关联映射：在多的一端加入一个外键指向一的一端，它维护的关系是一指向多
   ```

3. 多对多

   ```
   在操作和性能方面都不太理想，所以多对多的映射使用较少，实际使用中最好转换成一对多的对象模型；hibernate会为我们创建中间关联表，转换成两个一对多
   ```



### 2.1、单向一对一（one-to-one）

​	两个对象之间一对一的关系，例如：husband（丈夫）-wife(妻子） User和Account，一个用户对应一个账户,有两种策略可以实现一对一的关联映射

#### 2.1.1、 主键关联(不重要)

##### 2.1.1.1、核心思想

​	即让两个对象具有相同的主键值，以表明它们之间的一一对应的关系；数据库表不会有额外的字段来维护它们之间的关系，仅通过表的主键来关联,即其中一个表的主键参照另外一张表的主键而建立起一对一关联关系

##### 2.1.1.2、 配置方式

```
配置文件都用one-to-one，在辅表的one-to-one的属性里要加constrained="true"表示受到约束。所以，将辅表的id改成foreign然后加上属性参数
```

##### 2.1.1.3、示例代码

1. ##### 对象模型

   ![](http://opzv089nq.bkt.clouddn.com/17-7-7/16122517.jpg)	

2. ##### Wife类与配置文件

   1. java类

      ```
      private String wid;
      private String name;
      ```

   2. hbm.xml配置

      ```
       <class name="Wifi" table="TB_WIFI">
              <id name="wid" column="WID">
                  <generator class="uuid"/>
              </id>
              <property name="name" column="NAME"/>
       </class>
      ```

3. ##### Husband类与配置

   1. java类

      ```
      private String hid;
      private String name;
      private Wifi wifi;
      ```

   2. hbm.xml配置

      ```
       <class name="Husband" table="TB_HUSBAND">
              <id name="hid" column="HID">
                  <generator class="foreign">
                      <param name="property">wifi</param>
                  </generator>
              </id>
              <property name="name" column="NAME"/>
                 constrained="true"表示这个属性（name="wifi"）对应类型的表（wifi表）对Husband表形成了外键约束,
              <one-to-one name="wifi" class="Wifi" constrained="true"/>
          </class>
      ```

   3. 测试代码

      ```
      //使用默认的resources下Hibernate.cfg.xml文件加载
      Configuration cfg = new Configuration().configure();
      //创建会话工厂类
      SessionFactory factory = cfg.buildSessionFactory();
      Session session = null;
      Transaction transaction = null;
      //打开session 相当于jdbc的connection对象
      session = factory.openSession();
      //开启事务
      transaction = session.beginTransaction();
      //初始化数据
      Husband husband = new Husband();
      husband.setName("老王");
      Wifi wifi = new Wifi();
      wifi.setName("呵呵");
      wifi.setHusband(husband);
      husband.setWifi(wifi);
      //持久化对象
      session.save(husband);
      //提交事务
      transaction.commit();
      //关闭session
      session.close();
      ```

      ```
      查询是通过主键外键的类去查询
      核心代码
      Husband husband = em.find(Husband.class, "402882215d1d84d8015d1d84da0e0000");
      输出结果
      Husband{hid='402882215d1d84d8015d1d84da0e0000', name='老王', wifi=Wifi{wid='402882215d1d84d8015d1d84da0e0000', name='呵呵'}}
      ```

2.1.1.4、总结

1. 由于采用主键关联方式,其实就是通过主键关联到两张表,其关联的几率的主键值必须保持同步,这也就意味着我们只需要设定一张表比如(wifi表)设定了主键生成器,而另外一张表,Husband表的主键与他具有相同的主键值,

2. 虽然在Husband类和Wife类之间有互相引用关系，但是数据库端Husband和Wife表没有任何参照关系，只是当我们保存User对象时，若Husband中的wife属性值不为NULL，此时hibernate就会以wife.hbm.xml中使用的主键生成器生成的主键值作为向Husband表和wife表中插入记录的主键值，此时保持了关联记录的主键值的同步（由输出的SQL语句可知这是由Hibernate处理的，数据库端并不知情）

3. <one-to-one>中的constrained属性(关联约束)

   1. constrained属性值默认为false，当constrained为false时，生成的两个表之间无任何参照关系，并且使用的是立即加载策略,如果将constrained属性值改为true

   2. 如果插入数据时表不存在

      1. 若wife.hbm.xml中的constrained为true，建立两表并建立参照关系，wifi表中的id将是参照Husband表中id的外键（若删除上面的wife.setHusbnad(husband)自 然就会抛出异常啦，因为Wifi的id参照的是Husband中的id，并且两者主键值同步）；生成的两表结构及参照关系为
      2. 若husband.hbm.xml中的constrained为true建立两表并建立参照关系 Husband表中的id将参照wifi表中的id,那此时保存的对象应该是wifi对象
      3. 若两者的constrained都为true，虽然会在数据库中建立两个相互参照的表wife和husband表，但是保存对象时就会抛异常导致插入记录失败.

   3. 如果插入数据时表已存在

      1. 若wifi.hbm.xml中的<one-to-one>constrained为true，则当wifi=session.get(Wifi.class, 1L);延迟加载Wifi对象中的Husband。

      2. 若husband.hbm.xml中<one-to-one>的constrained为true，则当husband=session.get(Husband.class, 1L);延迟加载IdCard对象中的user。

      3. 若wifi.hbm.xml和husband.hbm.xml中的<one-to-one>constrained为true，就是上述两种情况的综合，无论查询哪类对象都会延迟加载它引用的一方

         ​

#### 2.1.2、唯一外键关联

##### 核心思想

​	外键关联，本来是用于多对一的配置，但是加上唯一的限制之后（采用<many-to-one>标签来映射，指定多的一端unique为true，这样就限制了多的一端的多重性为一），也可以用来表示一对一关联关系，其实它就是多对一的特殊情况

比如一个丈夫对应多个老婆,但领证的只有一个

1. 对象模型

   ![](http://opzv089nq.bkt.clouddn.com/17-7-8/58251018.jpg)

2. 关系模型

   ![](http://opzv089nq.bkt.clouddn.com/17-7-8/69530265.jpg)

3. Husband类与配置

   ```
   private String hid;
   private String name;	
   ```

   ```
     <class name="Husband" table="TB_HUSBAND">
           <id name="hid" column="HID">
               <generator class="uuid" />
           </id>
           <property name="name" column="NAME"/>
       </class>
   ```

4. Wife类与配置

   ```
   private String wid;

   private String name;

   private Husband Husband;
   ```

   ```
   <class name="Wifi" table="TB_WIFI">
           <id name="wid" column="WID">
               <generator class="uuid"/>
           </id>
           <property name="name" column="NAME"/>
           <many-to-one name="husband" unique="true"/>
   </class>
   ```

5. 测试代码

   ```
   核心代码 保存对象
   Husband husband = new Husband();
   husband.setName("老王");
   session.save(husband);

   Wifi wifi = new Wifi();
   wifi.setName("空空");
   wifi.setHusband(husband);
   session.save(wifi);
   ```

   ```
   查询对象
   Wifi wifi = session.find(Wifi.class, "402882215d1de03e015d1de040460001");
   System.out.println(wifi.toString());
   输出结果
   Wifi{wid='402882215d1de03e015d1de040460001', name='空空', husband=Husband{hid='402882215d1de03e015d1de0403d0000', name='老王'}}
   ```

6. 注意 因为一对一的主键关联映射扩展性不好，当我们的需要发生改变想要将其变为一对多的时候变无法操作了，所以我们遇到一对一关联的时候经常会采用唯一外键关联来解决问题，而很少使用一对一主键关联

## 2.2、双向一对一

#### 2.2.1、核心思想

```
 	双向关联映射与单向关联映射的原理是一样的，双向关联映射并不影响存储，只影响加载。所以，双向关联映射和单向关联映射的关系模型是一样的即数据库的表结构是一样的
```

#### 2.2.2、对象模型

​	![](http://opzv089nq.bkt.clouddn.com/17-7-8/83791652.jpg)	

#### 2.2.3、关系模型

​	![](http://opzv089nq.bkt.clouddn.com/17-7-8/11996848.jpg)			

#### 2.2.4、配置信息

1. Husband类和配置

   ```
   private String hid;
   private String name;
   private Wifi wifi;
   ```

   ```
   <class name="Husband" table="TB_HUSBAND">
     <id name="hid" column="HID">
         <generator class="uuid"/>
     </id>
     <property name="name" column="NAME"/>
          <!--cascade="all" 表示级联操作-->
         <!--property-ref="hub" 把被关联实体主键字段作为关联字段。有了property-ref，就可以
   				通过它指定被关联实体主键以外的字段作为关联字段,
   				注意加上此字段否则通过Husband无法查找WiFi对象-->
     <one-to-one name="wifi" cascade="all" property-ref="hub" />
   </class>
   ```

   ```
   <!--cascade="all"-->
   表示操作级联到子实体
     1.save-update:在保存和当前一端数据时,另一端数据可以一起保存.如上,假如Student.hbm.xml中设置了 cascade="save-update",那么在保存数据时,只需要保存Husband就可以,hibernate会自动把它相关联的另一端的Wifi的数据保存起来.
     2.none:不级联(不写就是默认不级联)
     3.delete:删除级联(不能在多的一端执行)
     4.all:表示所有操作都级联
   注意事项
     1.cascade是级联操作,使得在一段操作数据时,可以级联操作在另外一端的数据
     2. 在多对一的关系中,多的一端不能操作级联为delete,一般在多的一端设为save-update
     3. 在一对多的关系中,如果一的一端设置为delete,多的一端不能指明外键为空
   ```

   ​

2. Wifi类和配置

   ```
   private String wid;
   private String name;
   private Husband husband;
   ```

   ```
       <class name="Wifi" table="TB_WIFI">
           <id name="wid" column="WID">
               <generator class="uuid"/>
           </id>
           <property name="name" column="NAME"/>
           <many-to-one name="husband"  column="HID" not-null="true" unique="true" cascade="all">
           </many-to-one>
       </class>
   ```

#### 2.2.6、示例代码

1. 核心java代码

   ```
   核心代码
   Husband husband = new Husband();
   husband.setName("老王");
   Wifi wifi = new Wifi();
   wifi.setName("波多野结衣");
   husband.setWifi(wifi);
   wifi.setHusband(husband);
   session.save(wifi);
   ```

   ​

2. Hibernate sql语句

   ```
   Hibernate: 
       insert 
       into
           TB_HUSBAND
           (NAME, HID) 
       values
           (?, ?)
    Hibernate: 
       insert 
       into
           TB_WIFI
           (NAME, husband, WID) 
       values
           (?, ?, ?)    
   ```

3. 查询

   ```
   Husband husband = session.get(Husband.class, "402882215d2326e7015d2326e9280000");
   Wifi wifi = session.get(Wifi.class, "402882215d23286e015d2328704e0000");
   String name = wifi.getHub().getName();
   注意观察wifi对象的内存
   ```

   ![](http://opzv089nq.bkt.clouddn.com/17-7-9/58755613.jpg)

   ![](http://opzv089nq.bkt.clouddn.com/17-7-9/29037371.jpg)

4. 删除操作

   ```
   Husband husband = session.get(Husband.class, "402882215d2326e7015d2326e9280000");
   session.delete(husband);
   Hibernate: 
       delete 
       from
           TB_WIFI 
       where
           WID=?
   Hibernate: 
       delete 
       from
           TB_HUSBAND 
       where
           HID=?
   ```

   ```
   Wifi wifi = session.get(Wifi.class, "402882215d23286e015d2328704e0000");
   session.delete(wifi);
   Hibernate: 
       delete 
       from
           TB_WIFI 
       where
           WID=?
   Hibernate: 
       delete 
       from
           TB_HUSBAND 
       where
           HID=?
   ```

#### 2.2.7、说明

```
唯一外键关联较主键关联映射的好处就是，万一哪天需求变了，这两个对象的关系由一对一变为多对一，那么直接把外键唯一的约束去掉就行
```

#### 2.2.8 外键关联总结

```
1、在有外键的一方，可以维护关联关系，可以建立关联关系，同样也可以解除关联关系，可以任意删除本对象，如果在hbm.xml中设置了cascade="delete"，也可以删除关联对象

2、在没有外键的一方，不可以维护关联关系，所有无法建立关联关系，也无法解除关联关系。在删除过程中，如有没有外键值对应本条数据，可以成功删除，否则会抛出异常
```

## 2.3、多对一与一对多

#### 2.3.1、学习要点

1. ##### 一对多单向外键关联(XML/Annotation)

2. ##### 一对多双向外键关联(XML/Annotation)

3. ##### 多对一单向外键关联(XML/Annotation)

4. ##### 懒加载和积极加载

#### 2.3.2、  什么是单向,什么是双向

| 单向/双向        | User实体类中是否有List<Order> orders | Order实体类中是否有User user |
| ------------ | ----------------------------- | --------------------- |
| 单向多对一        | 无                             | 有                     |
| 单向一对多        | 有                             | 无                     |
| 双向一对多（双向多对一） | 有                             | 有                     |

### 2.4、多对一单向外键关联

​	注意多对一关联是多方持有一方的引用。例如:去京东购物，那么一个京东用户可以对应多个购物订单

#### 2.4.4、配置信息

##### 2.4.4.1、对象模型

​	![](http://opzv089nq.bkt.clouddn.com/17-7-9/7855323.jpg)

##### 2.4.4.2、关系模型

​	![](http://opzv089nq.bkt.clouddn.com/17-7-9/80254981.jpg)

##### 2.3.4.3、user类与配置

```
    private String userId;
    private String username;
    private String password;
```

```
  <class name="User" table="TB_USER">
        <id name="userId" column="USER_ID">
            <generator class="uuid"/>
        </id>
        <property name="username" column="USERNAME"/>
        <property name="password" column="PASSWORD"/>
    </class>
```

##### 2.3.4.3、Order类与配置

```
 private String orderId;
 private BigDecimal price;
 private User user;
```

```
<class name="Order" table="TB_ORDER">
        <id name="orderId" column="ORDER_ID">
            <generator class="uuid"/>
        </id>
        <property name="price" type="big_decimal" column="PRICE" precision="10" scale="2" />

        <many-to-one name="user" column="USER_ID" not-null="true" cascade="all"/>
    </class>
```

##### 2.3.4.4、测试代码

1. 测试保存

   ```
   1.核心Java代码
     User user = new User();
     user.setUsername("zhangwei");
     user.setPassword("123456");
     for (int i = 0; i < 10; i++) {
         Order   order  = new Order(new BigDecimal(50.00);
         order.setUser(user);
     }
   ```

   ```
   使用<many-to-one>元素进行多对一关联关系配置
   name属性  指定类的属性名，
   column属性 指定库表字段名，，
   not-null属性 指定属性是否允许为空，
   lazy 属性, 默认懒加载
   cascade属性 指定是否级联保存和更新：save-update、delete、all、none
   ```

### 2.5、一对多单向外键关联

​	简单说就是和之前多对一相反，之前是多方持有一方的引用，而一对多关联关系是一方持有多方的集合的引用，注意区别：这里是持有多方的集合。

#### 2.5.1、对象模型

![](http://opzv089nq.bkt.clouddn.com/17-7-9/88438253.jpg)

#### 2.5.2、关系模型

![](http://opzv089nq.bkt.clouddn.com/17-7-9/98435614.jpg)

#### 2.5.3、配置信息

1. User类配置

   ```
   private String userId;
   private String username;
   private String password;
   private List<Order> orders;
   ```

   ```
    <class name="User" table="TB_USER">
           <id name="userId" column="USER_ID">
               <generator class="uuid"/>
           </id>
           <property name="username" column="USERNAME"/>
           <property name="password" column="PASSWORD"/>
           
           <list name="orders" table="TB_ORRDER" cascade="all">
           	//数据库外键列
               <key column="USER_ID"/>
               //数据库list索引列名称
               <index column="ORDER_INDEX"/>
               <one-to-many class="Order"/>
           </list>
       </class>
   ```

   ​

2. Order类与配置

   ```
   private String orderId;
   private BigDecimal price;
   ```

   ```
    <class name="Order" table="TB_ORDER">
           <id name="orderId" column="ORDER_ID">
               <generator class="uuid"/>
           </id>
           <property name="price" type="big_decimal" column="PRICE" precision="10" scale="2" length="10"/>
       </class>
   ```

#### 2.5.4、示例代码

1. 建表语句

   ```mysql
   Hibernate: 
       create table TB_ORDER (
          ORDER_ID varchar(255) not null,
           PRICE decimal(10,2),
           USER_ID varchar(255),
           ORDER_INDEX integer,
           primary key (ORDER_ID)
       ) engine=InnoDB

   Hibernate: 
       create table TB_USER (
          USER_ID varchar(255) not null,
           USERNAME varchar(255),
           PASSWORD varchar(255),
           primary key (USER_ID)
       ) engine=InnoDB
   Hibernate: 
      alter table TB_ORDER 
          add constraint FKfef3qoeyl8ksa381j30wplrj5 
          foreign key (USER_ID) 
          references TB_USER (USER_ID)
   ```

2. 添加对象

   ```mysql
   #Java代码   
     User user = new User();
     user.setUsername("zhangwei");
     user.setPassword("123456");
     List<Order> orders = new ArrayList<>();
     orders.add(new Order(new BigDecimal(50.00)));
     orders.add(new Order(new BigDecimal(50.01)));
     user.setOrders(orders);
     session.save(user);

   # SQL语句   
   Hibernate: 
       insert 
       into
           TB_USER
           (USERNAME, PASSWORD, USER_ID) 
       values
           (?, ?, ?)
   Hibernate: 
       insert 
       into
           TB_ORDER
           (PRICE, ORDER_ID) 
       values
           (?, ?)
   Hibernate: 
       insert 
       into
           TB_ORDER
           (PRICE, ORDER_ID) 
       values
           (?, ?)
   Hibernate: 
       update
           TB_ORDER 
       set
           USER_ID=?,
           ORDER_INDEX=? 
       where
           ORDER_ID=?
   Hibernate: 
       update
           TB_ORDER 
       set
           USER_ID=?,
           ORDER_INDEX=? 
       where
           ORDER_ID=?
   ```

3. 查找对象

   ```
   Java代码
   User user = session.get(User.class, "402882215d2701fe015d270200b90000");
   输出结果
   User{userId='402882215d2701fe015d270200b90000', username='zhangwei', password='123456', orders=[Order{orderId='402882215d2701fe015d270200cb0001', price=50.00}, Order{orderId='402882215d2701fe015d270200cb0002', price=50.01}]}
   ```

4. 删除对象

   ```mysql
   #Java代码
   User user = session.get(User.class, "402882215d2701fe015d270200b90000");
   session.remove(user);
   #Sql语句代码
   Hibernate: 
       update
           TB_ORDER 
       set
           USER_ID=null,
           ORDER_INDEX=null 
       where
           USER_ID=?
   Hibernate: 
       delete 
       from
           TB_ORDER 
       where
           ORDER_ID=?
   Hibernate: 
       delete 
       from
           TB_ORDER 
       where
           ORDER_ID=?
   Hibernate: 
       delete 
       from
           TB_USER 
       where
           USER_ID=?
   ```

### 2.5、双向外键关联(重要)

​	类似之前的一对一双向外键关联，也是互相持有对方的引用，故也叫双向一对多自身关联。多方持有一方的引用，反过来，一方也持有多方的集合。

#### 2.5.1、对象模型

​	![](http://opzv089nq.bkt.clouddn.com/17-7-9/51436696.jpg)

#### 2.5.2、关系模型

![](http://opzv089nq.bkt.clouddn.com/17-7-9/45692714.jpg)

#### 2.5.3、配置信息

1. Category类配置

   ```
   private String cid;
   private String name;
   private Set<CategorySub> subs;
   ```

   ```
    <class name="Category" table="TB_CATEGORY">
           <id name="cid" column="CID">
               <generator class="uuid"/>
           </id>
           <property name="name" column="NAME" />
           <set name="subs" table="TB_CATEGORY_SUB" cascade="all" inverse="true">
               <key column="CID"/>
               <one-to-many class="CategorySub"/>
           </set>
       </class>
   ```

2. CategorySub类配置

   ```
       private String sname;
       private String sid;
       private Category category;
   ```

   ```
    <class name="CategorySub" table="TB_CATEGORY_SUB">
           <id name="sid" column="SID">
               <generator class="uuid"/>
           </id>
           <property name="sname" column="SNAME"/>
           <many-to-one name="category" column="CID" cascade="all"/>
       </class>
   ```

2.5.3、示例代码

1. 建表语句

   ```
   Hibernate: 
       create table TB_CATEGORY (
          CID varchar(255) not null,
           NAME varchar(255),
           primary key (CID)
       ) engine=InnoDB
   Hibernate: 
       create table TB_CATEGORY_SUB (
          SID varchar(255) not null,
           SNAME varchar(255),
           CID varchar(255),
           primary key (SID)
       ) engine=InnoDB
   Hibernate: 
       alter table TB_CATEGORY_SUB 
          add constraint FKj1mug6ld2o5lpcgo1b2y11l7x 
          foreign key (CID) 
          references TB_CATEGORY (CID)
   ```

2. 保存对象

   ```
       String[] title = {"平板", "电脑", "摄影", "摄像", "娱乐影音", "路由器", "手机配件", "周边配件", "玩3C"};

           Category category = new Category();
           category.setName("电子产品");

           Set<CategorySub> subs = new HashSet<>();
           for (int i = 0; i < 9; i++) {
               CategorySub sub = new CategorySub(title[i]);
               subs.add(sub);
           }
           category.setSubs(subs);
           session.save(category);
           
           
   Hibernate: 
       insert 
       into
           TB_CATEGORY
           (NAME, CID) 
       values
           (?, ?)
   Hibernate: 
       insert 
       into
           TB_CATEGORY_SUB
           (SNAME, CID, SID) 
       values
           (?, ?, ?)
   Hibernate: 
       insert 
       into
           TB_CATEGORY_SUB
           (SNAME, CID, SID) 
       values
           (?, ?, ?)
   Hibernate: 
       insert 
       into
           TB_CATEGORY_SUB
           (SNAME, CID, SID) 
       values
           (?, ?, ?)
   Hibernate: 
       insert 
       into
           TB_CATEGORY_SUB
           (SNAME, CID, SID) 
       values
           (?, ?, ?)
   Hibernate: 
       insert 
       into
           TB_CATEGORY_SUB
           (SNAME, CID, SID) 
       values
           (?, ?, ?)
   	...
   ```

#### 2.6、多对一和一对多总结

1. 一对多,多对一单向关联
   1. 多对一时候，多方设置EAGER,一方设置LAZY，也就是说，如果是多对一，多方控制一方，那么多方设置积极加载，一方无需多余配置，
   2. 反过来，如果是一对多关系，一方控制多方，那么一方设置懒加载，多方无需多余配置。
2. 一对多或者多对一双向外键关联
   1. 在一的一端的集合上使用<key>，在对方表中加入一个外键指向一的一端。
   2. 在多的一端要采用<many-to-one>标签<key>标签指定的外键字段必须和<many-to-one>指定的外键字段一致，否则会引起字段的错误。
   3. 在一的一端维护一对多的关联关系，hibernate会发多余的update语句，所有我们一般在多的一端来维护这种关系，因此通常在set 标签上添加inverse=“true” 属性来提高系统效率。
3. 在多对一，一对一关联关系中，Hibernate默认设置多方的inverse=true，即多方为被控方，一方的inverse=false

### 2.6、多对多（many-to-many）

### 2.6.1、核心思想

```
其中一个多方持有另一个多方的集合对象
要实现多对多关系，必须有一张中间表 用于维护两方之间的关系
```

```
CREATE TABLE TB_TEACHER (
SID INT(6),
TID INT(6),
PRIMARY KEY(SID,TID)
)
```



### 2.6.2、单向多对多

#### 2.6.2.1、说明

```
权限和角色的关系是一种多对多的关系，一个权限可以分配给多个角色，一个角色拥有多个权限。
```

#### 2.6.2.2、对象模型

![](http://opzv089nq.bkt.clouddn.com/17-7-11/92223778.jpg)



#### 2.6.2.3、关系模型

![](http://opzv089nq.bkt.clouddn.com/17-7-11/58584552.jpg)

#### 2.6.3.3、配置信息

1. privilege类配置

   ```
   private Integer privilegeId;
   private String pname;
   ```

2. Role类配置

   ```
   private Set<Privilege> privileges;
   private int roleId;
   private String roleName;
   ```

   ​



创建表语句

```
CREATE TABLE TB_ROLE(                        #角色表  
   ROLE_Id INT PRIMARY KEY AUTO_INCREMENT ,  #角色ID  
   ROLE_NAME VARCHAR(20) NOT NULL            #角色名称  
) ;  
  
CREATE TABLE TB_PRIVILEGE (                  #权限表  
   PRIVILEGE_ID INT PRIMARY KEY ,            #权限ID  
   PRIVILEGE_NAME VARCHAR(45) NOT NULL       #权限表  
) ;  
  
CREATE TABLE TB_ROLE_PRIVILEGE(              #角色_权限中间表  
   ROLE_ID INT ,  
   PRIVILEGE_ID INT ,  
   PRIMARY KEY (ROLE_ID,PRIVILEGE_ID) ,  
   CONSTRAINT fk_privilege_role FOREIGN KEY(ROLE_ID) REFERENCES TB_ROLE(ROLE_ID) ,  
   CONSTRAINT fk_role_privilege FOREIGN KEY(PRIVILEGE_ID) REFERENCES TB_PRIVILEGE(PRIVILEGE_ID)   
) ;
```



### 2.6.3、双向多对多

#### 2.6.3.1、对象模型

![](http://opzv089nq.bkt.clouddn.com/17-7-11/86234682.jpg)	

#### 2.6.3.2、关系模型

![](http://opzv089nq.bkt.clouddn.com/17-7-10/88342368.jpg)

#### 2.6.3.3、配置信息

1. Student类的配置

   ```java
       private Integer sid;

       private String sname;

       private Set<Teacher> teachers;
   ```

   ```xml
    <class name="Student" table="TB_STUDENT">
           <id name="sid" column="SID" length="6">
               <generator class="native"/>
           </id>
           <property name="sname" column="SNAME"/>
           <set name="teachers" table="TB_TEACHER_STUDENT" cascade="all">
               <key column="SID"/>
               <many-to-many column="TID" class="Teacher"/>
           </set>
       </class>
   ```

2. Teacher类的配置

   ```java
   private Integer tid;
   private String tname;
   private Set<Student> students;
   ```

   ```xml
    <class name="Teacher" table="TB_TEACHER">
           <id name="tid" column="TID" length="6">
               <generator class="native"/>
           </id>
           <property name="tname" column="TNAME"/>
           <set name="students" table="TB_TEACHER_STUDENT" cascade="all">
               <key column="TID"/>
               <many-to-many column="SID" class="Student"/>
           </set>
       </class>
   ```

### 2.6.3.4、 测试代码

1. 建表语句

   ```sql
   Hibernate: 
       create table TB_TEACHER (
          TID integer not null auto_increment,
           TNAME varchar(255),
           primary key (TID)
       ) engine=InnoDB
   Hibernate: 
       create table TB_TEACHER_STUDENT (
          SID integer not null auto_increment,
           TID integer not null,
           primary key (TID, SID)
       ) engine=InnoDB
   ```

2. 添加对象

   ```sql
      #java代码
      Set<Teacher> teachers = new HashSet<>();
           for (int i = 0; i < 3; i++) {
               Teacher teacher = new Teacher();
               teacher.setTname("老师" + i);
               teachers.add(teacher);
           }
           Student s = new Student();
           s.setSname("小明");
           s.setTeachers(teachers);
           session.save(s);

   Hibernate: 
       insert 
       into
           TB_STUDENT
           (SNAME) 
       values
           (?)
   Hibernate: 
       insert 
       into
           TB_TEACHER
           (TNAME) 
       values
           (?)
   Hibernate: 
       insert 
       into
           TB_TEACHER
           (TNAME) 
       values
           (?)
   Hibernate: 
       insert 
       into
           TB_TEACHER
           (TNAME) 
       values
           (?)
   Hibernate: 
       insert 
       into
           TB_TEACHER_STUDENT
           (SID, TID) 
       values
           (?, ?)
   Hibernate: 
       insert 
       into
           TB_TEACHER_STUDENT
           (SID, TID) 
       values
           (?, ?)
   Hibernate: 
       insert 
       into
           TB_TEACHER_STUDENT
           (SID, TID) 
       values
           (?, ?)
   ```

3. 查询对象

   ```
   Student student = session.get(Student.class, 1);
   System.out.println(student.toString());
   Teacher teacher = session.get(Teacher.class, 1);
   System.out.println(teacher.toString());
   ```

4. 删除对象

   ```
   1. 如果cascade不管主控方设置还是被控方设置成 all, delete等与delete级联删除有关即可，两端以及中间表的记录都会被删除，通常这样的需要是很少的，因此，如果你要这样的情况，只要简单设置成all, delete就可以轻松的将关系以及两端的记录删除的干干净净。

   2. 只想删除某一端的记录以及中间的表的关联信息。 这种需求通常是很常见的。这个时候cascade的设置是除与delete有关的任何级联约束。

   2.1. 多对多 主控方删除(可以删除中间表记录)
   	Student student = session.get(Student.class, tid);
   	session.delete(student);

   2.2. 多对多 被控方删除(无法删除中间表记录)
   	
   // 如果想既想删除被控方，双想删除关联
   Transaction transaction = session.beginTransaction();

   Teacher teacher =session.get(Teacher.class, 1);

   Set<Student> cs = teacher.getStudents();
   for (Student student : cs) {
     student.getTeachers().remove(teacher);
   }
   session.delete(teacher);
   transaction.commit();
   ```



### 2.6.3.4、多对多时用两个一对多来处理

### 2.6.3.5、多对多总结

1. 两个实体类，三个表，第三个表不需要实体类
2. 保存和修改、删除的时候，也不需要操作中间表，操作两个实体类中的一个，就可以向中间表保存或修改或删除数据。
3. 查询的时候，不需要查询中间表，通过查询另外两个实体类中的一个，调用getter方法，都可以得到中间表的数据
4. 有一个表**只有两个字段**，构成联合主键，分别指向另两个表的主键，**它没有自己的独立的自动递增主键**。（映射文件里的set标签或bag标签里面的子元素的两个column是第三个表的两个字段）
5. 另外，多对多的模型，可以通过两个多对一实现，所以多对多的概念，一定要分清。例如用户和角色是多对多，但是实现方式可以是两个多对一，也可以是多对多。区分的办法就是通过表的字段。如果用多对多实现，那么映射文件里set或者bag元素后面配一个table="中间表"