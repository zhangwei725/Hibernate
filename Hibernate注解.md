# Hibernate注解

## 1、类级别注解

### 1.1、 @Entity    映射实体类

```
@Entity(name="tableName") - 必须，注解将一个类声明为一个实体bean。
属性：name - 可选，对应数据库中的一个表。若表名与实体类名相同，则可以省略
```

### 1.2、 @Table   映射数句库表

```
@Table(name="",catalog="",schema="")  - 可选，通常和@Entity 配合使用，只能标注在实  体的 class定义处，表示实体对应的数据库表的信息。
属性：
name - 可选，表示表的名称，默认地，表名和实体名称一致，只有在不一致的情况下才需要指定表名
catalog - 可选，表示Catalog名称，默认为Catalog("").
schema - 可选 , 表示 Schema名称 , 默认为Schema("").
```

# 2、属性级别注解

| 属性         | 说明                                       | 默认值  |
| ---------- | ---------------------------------------- | ---- |
| @Id        | 映射生成主键                                   | 必须   |
| @Column    | 映射表的列                                    | 可选   |
| @Transient | 表示该属性并非一个到数据库表的字段的映射，ORM框架将忽略该属性，如果一个属性并非数据库表的字段映射，就务必将其标示为@Transient，否则ORM 框架默认其注解为@Basic | 可选   |
| @Basic     | 表示一个简单的属性到数据库表的字段的映射,对于没有任何标注的 getXxxx() 方法,默认即为@Basic | 可选   |
| @Temporal  | 用于定义映射到数据库的时间精度                          | 可选   |
| @Lob       | @Lob注解属性将被持久化为 Blog或 Clob类型。具体的java.sql.Clob, Character[], char[]和 java.lang.String将被持久化为 Clob类型. java.sql.Blob,Byte[], byte[]和 serializable type将被持久化为 Blob类型 | 可选   |
| @Version   | 定义乐观锁                                    | 可选   |

### 2.1、 主键相关注解

#### 2.1.1、 与主键相关注解

##### 2.1.2 、@Id(必选)

```
定义了映射到数据库表的主键的属性，一个实体只能有一个属性被映射为主键，
```

##### 2.1.2、 @GeneratedValue(strategy=,generator="")(可选)

1. 说明

   ```
   用于定义主键生成策略
   ```


2. 可选值

   | 可选属性      | 说明                        | 默认值  |
   | --------- | ------------------------- | ---- |
   | strategy  | 注解生成策略                    | AUTO |
   | generator | 表示主键生成器的名称，这个属性通常和ORM框架相关 | 无    |

##### 2.1.3、 Strategy - 表示主键生成策略

| 可选属性                     | 说明                                       |
| ------------------------ | ---------------------------------------- |
| GenerationType.AUTO      | 根据底层数据库自动选择（默认），若数据库支持自动增长类型，则为自动增长。     |
| GenerationType.INDENTITY | 根据数据库的Identity字段生成,支持DB2、MySQL、MS、SQL Server、SyBase与HyperanoicSQL数据库的Identity类型主键。 |
| GenerationType.SEQUENCE  | 注解声明了一个数据库序列、支持Oracle DB2。               |
| GenerationType.TABLE     | 使用一张单表管理主键值 结合@TableGenerator使用          |

2.1.3.1 示例代码

1. GenerationType.AUTO            

   ```
   示例代码
   @Id
   @GeneratedValue
   private Integer userId;
   ```

2. GenerationType.INDENTITY

   ```
   @Id
   @GeneratedValue(strategy=GenerationType.INDENTITY)
   private Integer userId;
   ```

3. GenerationType.SEQUENCE 

   ```
   1. 数据库中先定义一个Oracle序列，例如:SEQ_USER_ID
       create sequence SEQ_USER_ID
       start with 1
       increment by 1
       nomaxvalue
       nominvalue
       nocycle
       nocache;
   2. 在Entity实体类中通过使用注解@SequenceGenerator
   @Entity(name = "TB_USER")
   @SequenceGenerator(name = "generator_user_id", sequenceName = "SEQ_USER_ID", allocationSize = 1)
   public class User {
     @Id
     @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "generator_user_id")
     private Integer userId;
   }

   3 参数说明
   name - 表示该表主键生成策略名称，它被引用在@GeneratedValue中设置的“gernerator”值中。
   sequenceName - 表示生成策略用到的数据库序列名称。
   initialValue - 表示主键初始值，默认为0.
   allocationSize - 每次主键值增加的大小，例如设置成1，则表示每次创建新记录后自动加1，默认为50.
   ```

4. GenerationType.TABLE

   ```
   @Id
   @TableGenerator(name="ID_USER_GENERATOR",
               table="jpa_id_generators",
               pkColumnName="PK_NAME",
               pkColumnValue="USER_ID",
               valueColumnName="PK_VALUE",
               allocationSize=100)
       @GeneratedValue(strategy=GenerationType.TABLE,generator="ID_USER_GENERATOR")
       private Integer userId;
      
   说明  很少使用 
   将当前主键的值单独保存到一个数据库的表中，主键的值每次都是从指定的表中查询来获得  
   这种方法生成主键的策略可以适用于任何数据库，不必担心不同数据库不兼容造成的问题 
   可选属性
   1. @TableGenerator(name="ID_USER_GENERATOR", --name 属性表示该主键生成的名称，它被引用在@GeneratedValue中设置的generator 值中(即这两个名字要一样） 
   2. pkColumnName="PK_NAME",      --pkColumnName 属性的值表示在持久化表中，该主键生成策略所对应键值的名称 
   3. pkColumnValue="USER_ID", --pkColumnValue 属性的值表示在持久化表中，该生成策略所对应的主键（跟pkColumnName属性可以确定唯一的一行，该行有很多列） 
   4. valueColumnName="PK_VALUE",  --valueColumnName 属性的值表示在持久化表中，该主键当前所生成的值，它的值将会随着每次创建累加，（再加这个属性能确定唯一的那个点）
   5. allocationSize=1)          --allocationSize 表示每次主键值增加的大小, 默认值为 50    
   ```

### 2.2 、其他相关注解

#### 2.2.1、 @CLOUNM

1. 可选值

   | 性名               | 说明                                       | 默认值       |
   | ---------------- | ---------------------------------------- | --------- |
   | unique           | 可选,是否在该列上设置唯一约束                          | false     |
   | nullable         | 可选,是否设置该列的值可以为空                          | true      |
   | insertable       | 可选,该列是否作为生成的insert语句中的一个列                | true      |
   | updatable        | 可选,该列是否作为生成的update语句中的一个列                | true      |
   | columnDefinition | 可选: 为这个特定列覆盖SQL DDL片段 (这可能导致无法在不同数据库间移植)比如我们想把Integer readNum 设为默认0，可使用此属性： INT DEFAULT 0 | 无         |
   | table            | 可选,定义对应的表                                | 默认为当前类对应表 |
   | length           | 可选,列长度                                   | 255       |
   | precision        | 可选,列十进制精度(decimal precision)             | 0         |
   | scale            | 可选,如果列十进制数值范围(decimal scale)可用,在此设置      | 0         |


1. 示例代码

   ```java
   @Column(
               name = "NAME",
               unique = true,
               nullable = false,
               insertable = false,
               updatable = false,
               length = 64
       )
       private String name;
   //只有在 decimal类型下才有效 精度控制
       @Column(
          	    name = "PRICE",
               precision = 10,
               scale = 2
       )
       private BigDecimal price;
   ```

#### 2.2.2、 @Basic

1. @Basic 表示一个简单的属性到数据库表的字段的映射,对于没有任何标注的 getXxxx() 方法,默认即为@Basic  

   | 属性       | 说明                                       | 默认值   |
   | -------- | ---------------------------------------- | ----- |
   | fetch    | 表示该属性的读取策略,有 EAGER 和 LAZY 两种,分别表示主支抓取和延迟加载 | EAGER |
   | optional | 表示该属性是否允许为null,                          | true  |

#### 2.2.2、 @Temporal

1. 示例代码

       @Column(name = "LAST_TIME")
       @Temporal(TemporalType.TIMESTAMP)
       private Timestamp lastTime;
       @Column(name = "CREATE_DATE")
       @Temporal(TemporalType.DATE)
       private Date createDate;


  ​       

  ```
  核心代码
  1. 将String类型的时间转化成数据库合适的时间
  	user.setCreateDate(new Date(new SimpleDateFormat("yyyy-MM-dd").parse("2016-01-01").getTime()));
  2. 获取系统当前时间
  	user.setCreateDate(new Date(System.currentTimeMillis()));


  ```

#### 2.2.3、 @Embedded

1. **一个实体类要在多个不同的实体类中进行使用，而本身又不需要独立生成一个数据库表**，这就是需要@Embedded、@Embeddable的

#### 2.2.3.4、 @MappedSuperclass

1. 可以将父类的JPA注解传递给子类,使子类能够继承超类的JPA注解

   ```
   @MappedSuperclass
   public class Parent() {

   }

   @Entity
   public class Child extends Parent {

   }
   ```

   ​

## 3、 关联映射的一些定义

#### 3.1、 说明

```
单向一对多：一方有集合属性，包含多个多方，而多方没有一方的引用
单向多对一：多方有一方的引用，一方没有多方的引用。
双向一对多：两边都有多方的引用，方便查询。
双向多对一：两边都有多方的引用，方便查询。
单向多对多：需要一个中间表来维护两个实体表
单向一对一：数据唯一，数据库数据也是一对一。
主键相同的一对一：使用同一个主键，省掉外键关联。
```

#### 3.2、 关系管理

```
单向：关系写哪边，就由谁管理。
双向：一般由多方管理。
```

#### 3.3、 关联映射的一些共有属性

fetch - 配置加载方式。取值有

​	Fetch.*EAGER* -  及时加载，多对一默认是Fetch.*EAGER* 

​	Fetch.*LAZY* - 延迟加载，一对多默认是Fetch.*LAZY*

cascade - 设置级联方式，取值有：

​	CascadeType.*PERSIST* - 保存

​	CascadeType.*REMOVE* - 删除

​	CascadeType.*MERGE* - 修改

​	CascadeType.*REFRESH* - 刷新

​	CascadeType.*ALL* - 全部

targetEntity - 配置集合属性类型

## 4、 一对一关联

### 4.1、概要

1. 说明

   ```
   外键表示了两个关系之间的相关联系。以另一个关系的外键作主关键字的表被称为主表，具有此外键的表被称为主表的从
   表。外键又称作外关键字。
   利用外键建立起来的一对一映射，一对一的连接了两张表，使其具有了唯一的映射关系。这种映射就叫作一对一外键映射
   表示一个一对一的映射
   ```

2. 分类

   ```
   一对一单向外键关联
   一对一双向主键关联
   一对一双向外键关联
   ```

### 4.2、单向外键关联 

1. 示例代码

   ```
   @OneToOne(cascade = CascadeType.ALL)
   @JoinColumn(name = "UID")
   private Account account;
   主表无变化
   ```

### 4.3、双向外键关联

1. 说明:

   ```
   	双方类上使用@OneToOne,但是需要注意的是，如果仅仅是这样的话，会生成两条外键约束，很没有必要，因此在被拥有方的@oneToOne注解中加入mappedBy属性并设置为拥有方的关联属性名即可mappedBy 它表示当前所在表和User的关系是定义在Account里面的user这个成员上面的，他表示此表是一对一关系中的从表，也就是关系是在user表中维护的，这是最重要的。User表是关系的维护者，有主导权，它有个外键指向user_id
   定义类之间的双向关系。如果类之间是单向关系，不需要提供定义，如果类和类之间形成双向关系，我们就需要使用这个属性进行定义，否则可能引起数据一致性的问题。
   ```

2. @JoinColumn


      @JoinColumn和@Column类似,他描述的不是一个简单字段,而一一个关联字段
      name:该字段的名称.由于@JoinColumn描述的是一个关联字段,默认的名称由其关联的实体决定.
      例如,实体Order有一个user属性来关联实体User,则Order的user属性为一个外键,
      其默认的名称为实体User的名称+下划线+实体User的主键名称

3. 示例代码

   1.  User表的配置

         ```
         @Id

         @GeneratedValue(generator = "hib-uuid")

         @GenericGenerator(name = "hib-uuid", strategy = "uuid")

         @Column(name = "USER_ID", length = 32)

         private String userId;

         @OneToOne(cascade = CascadeType.ALL)

         @PrimaryKeyJoinColumn

         private Account account;
         ```

   2. Account
          @Id
          @GeneratedValue(generator = "generator_uuid")
          @GenericGenerator(name = "generator_uuid", strategy = "uuid")
          @Column(name = "aid", length = 32)
          private String aid;
          
          @OneToOne(cascade = CascadeType.ALL)
          @JoinColumn(name = "U_ID")
          private User user;    

### 4.4、双向主键

1. 示例代码

   1. User类

      ```
      @Id
      @GeneratedValue
      @Column(name = "USER_ID")
      private Integer userId;
      @OneToOne(cascade = CascadeType.ALL)
      @PrimaryKeyJoinColumn
      private Account account;
      ```

   2.    Account类

         ```
         @Id
         @GenericGenerator(name = "foreignKey" ,//生成器名称
         strategy = "foreign",//使用hibernate的外键策略
         parameters = @Parameter(value = "user",name = "property"))//指定成员属性中的User所在类的主键为本类的主键,这里的参数属性name必须为"property"
         @GeneratedValue(generator = "foreignKey")//使用上述定义的id生成器
         @Column(name = "AID")
         private Integer aid;
         //如果试图不加此注解来形成单向关联，会抛出异常，因为设置了共享主键这里是否配置mapperBy放弃维护关联关系已失去作用。
         @OneToOne(cascade = CascadeType.ALL)
         @PrimaryKeyJoinColumn//如果不加此注解，hibernate会在数据库默认生成一条user_id属性
         private User user;
         ```


## 5、一对多(多对一)

### 5.1、一对多外键关键

1. 说明

   ```
   @OneToMany - 描述一个一对多的关联，该属性应该为集合类型，在数据库中并没有实际字段。
   ```

2. 单向一对多：一方有集合属性，包含多个多方，而多方没有一方的引用

3.  @OneToMany的属性

   1. targetEntity

      ```
      定义关系类的类型，默认是该成员属性对应的类类型，所以通常不需要提供定义。
      ```

   2. mappedBy

      ```
      定义类之间的双向关系。	如果类之间是单向关系，不需要提供定义，如果类和类之间形成双向关系，我们就需要使用这个属性进行定义，否则可能引起数据一致性的问题。该属性的值是“多”方class里的“一”方的变量名
      ```

   3. cascade

      ```
      该属性定义类和类之间的级联关系。定义的级联关系将被容器视为对当前类对象及其关联类对象采取相同的操作，而且这种关系是递归调用的。举个例子：Order 和OrderItem有级联关系，那么删除Order时将同时删除它所对应的OrderItem对象。而如果OrderItem还和其他的对象之间有级联关系，那么这样的操作会一直递归执行下去。

      cascade可选值

      CascadeType.PERSIST（级联新建）
      CascadeType.REMOVE（级联删除）
      CascadeType.REFRESH（级联刷新）
      CascadeType.MERGE（级联更新）中选择一个或多个
      CascadeType.ALL，表示选择全部四项。
      ```

   4. fatch

      ```
      可选择项包括：FetchType.EAGER和FetchType.LAZY。前者表示关系类(本例是OrderItem 类)在主类(本例是Order类)加载的时候同时加载，后者表示关系类在被访问时才加载。默认值是FetchType.LAZY。

      ```

   5. @JoinColumn(name = "指定映射表的外键")

4. 示例代码

   ```
   @OneToMany  默认会使用连接表做一对多关联
   添加@JoinColumn(name="xxx_id") 后，就会使用外键关联，而不使用连接表了
   ```

### 5.2、多对一外键关联

1. 说明

   ```
   多方有一方的引用，一方没有多方的引用。 关系维护在多方
   ```

2. @ManyToOne：

   1. 四个属性 

      ```
      targetEntity、cascade、fetch 和optional，前三个属性的具体含义和@OneToMany的同名属性相同，但@ManyToOne的fetch 属性默认值是FetchType.EAGER。
      ```

   2. optional属性

      ```
      定义该关联类是否必须存在，值为false 时，关联类双方都必须存在，如果关系被维护端不存在，查询的结果为null。值为true 时, 关系被维护端可以不存在，查询的结果仍然会返回关系维护端，在关系维护端中指向关系被维护端的属性为null。optional属性的默认值是true。optional 属性实际上指定关联类与被关联类的join 查询关系
      ```

3. 示例代码

   ```
   @ManyToOne(targetEntity=XXXX.class)   //指定关联对象
   @JoinColumn(name="")       //指定产生的外键字段名
   ```

### 5.2、双向一对多(多对一)

1. 说明

   ```
   双向一对多关系，一是关系维护端（owner side），多是关系被维护端（inverse side）。在关系被维护端需要通过@JoinColumn建立外键列指向关系维护端的主键列
   ```

2. 示例代码

   1. Order表的配置

      ```
      publicclass Order implements Serializable {
          @OneToMany(mappedBy="order",cascade = CascadeType.ALL, fetch = FetchType.LAZY)
          @OrderBy(value = "ID ASC")
          privateSet<OrderItem> orderItems = new HashSet<OrderItem>();
      }
      ```

3. OrderItem类配置

   ```
   publicclass OrderItem implements Serializable {
   	@ManyToOne(cascade=CascadeType.REFRESH,optional=false)
        @JoinColumn(name = "ORDER_ID")
   	private Order order;
   }
   ```

属性说明

1. @OneToMany的属性：

   ```
   1>targetEntity
   定义关系类的类型，默认是该成员属性对应的类类型，所以通常不需要提供定义。
    
   2>mappedBy
   定义类之间的双向关系。如果类之间是单向关系，不需要提供定义，如果类和类之间形成双向关系，我们就需要使用这个属性进行定义，否则可能引起数据一致性的问题。
   该属性的值是“多”方class里的“一”方的变量名
    
   3>cascade
   该属性定义类和类之间的级联关系。定义的级联关系将被容器视为对当前类对象及其关联类对象采取相同的操作，而且这种关系是递归调用的。举个例子：Order 和OrderItem有级联关系，那么删除Order时将同时删除它所对应的OrderItem对象。而如果OrderItem还和其他的对象之间有级联关系，那么这样的操作会一直递归执行下去。
    
   cascade的值只能从CascadeType.PERSIST（级联新建）、CascadeType.REMOVE（级联删除）、CascadeType.REFRESH（级联刷新）、CascadeType.MERGE（级联更新）中选择一个或多个。还有一个选择是使用CascadeType.ALL，表示选择全部四项。
    
   4>fatch
   可选择项包括：FetchType.EAGER和FetchType.LAZY。前者表示关系类(本例是OrderItem 类)在主类(本例是Order类)加载的时候同时加载，后者表示关系类在被访问时才加载。默认值是FetchType.LAZY。
    
   @JoinColumn(name = "ORDER_ID")注释指定OrderItem映射表的ORDER_ID列作为外键与Order 映射表的主键列关联。
   ```



## 6、多对多

### 6.1、 说明

```
其中一个多方持有另一个多方的集合对象
要实现多对多关系，必须有一张中间表 用于维护两方之间的关系

备注:在操作和性能方面都不太理想，所以多对多的映射使用较少，实际使用中最好转换成一对多的对象模型；hibernate会为我们创建中间关联表，转换成两个一对多
```

### 6.2、 属性说明

1. targetEntity

   ```
   表示多对多关联的另一个实体类的全名
   ```

2. mappedBy 

   ```
   用在双向关联中，把关系的维护权翻转 详细说明参照上面
   ```

3. @JoinTable 第三张表映射

   1. name

      ```
      就是指定了中间表的名字
      ```

   2.  JoinColumns

      ```
       @JoinColumn类型的数组，表示的是我这方在对方中的外键名称
      ```

   3. inverseJoinColumns

      ```
      @JoinColumn类型的数组，表示的是对方在我这放中的外键名称
      ```

### 6.3、单向多对多关联：

1. 示例代码

   ```
   在主控方加入@ManyToMany注解即可。
   ```

### 6.4、双向多对多关联：

1. 操作步骤

   1. 两个实体间互相关联的属性必须标记为@ManyToMany，
   2. 并相互指定targetEntity属性。
   3. 有且只有一个实体的@ManyToMany注解需要指定mappedBy属性，指向targetEntity的集合属性名称。
   4. 在主表一方指定第三张表

2. 示例代码

   1. Privilege类

      ```
      mappedBy = "privileges" 
      @ManyToMany(mappedBy = "privileges")
      private Set<Role> roles;
      ```

   2. role类

      ```
      @ManyToMany(cascade = CascadeType.ALL)
        //@JoinTable第三种
        //name = "TB_ROLE_PRI" 表示中间表的名字
       @JoinTable(name = "TB_ROLE_PRI",
       			//表示角色表的外键
                  joinColumns = @JoinColumn(name = "ROLE_ID"), 
               	//表示权限多表的外键
                  inverseJoinColumns = @JoinColumn(name = "PRIV_ID")
          )
      private Set<Privilege> privileges;
      ```

3. 备注

   1. 如果如果想多对多双方同时级联删除在另一端也加上joinColumns,inverseJoinColumns调换

      1. Privilege类

         ```
         @ManyToMany(mappedBy = "privileges")
          @JoinTable(name = "TB_ROLE_PRI",
                     joinColumns = @JoinColumn(name = "PRIV_ID"), 
                     inverseJoinColumns = @JoinColumn(name = "ROLE_ID")
             )
         private Set<Role> roles;
         ```

      2. role类

         ```
         @ManyToMany(cascade = CascadeType.ALL)
           //@JoinTable第三种
           //name = "TB_ROLE_PRI" 表示中间表的名字
          @JoinTable(name = "TB_ROLE_PRI",
          			//表示角色表的外键
                     joinColumns = @JoinColumn(name = "ROLE_ID"), 
                  	//表示权限多表的外键
                     inverseJoinColumns = @JoinColumn(name = "PRIV_ID")
             )
         private Set<Privilege> privileges;
         ```

   2. 去除系统自动创建表第四张表 在被控放使用mappedBy = "指向另外对己方的引用" 

      ```
      定义类之间的双向关系。如果类之间是单向关系，不需要提供定义，如果类和类之间形成双向关系，我们就需要使用这个属性进行定义，否则可能引起数据一致性的问题
      @ManyToMany(cascade = CascadeType.ALL)
        //name = "TB_ROLE_PRI" 表示中间表的名字
       @JoinTable(name = "TB_ROLE_PRI",
       			     //表示角色表的外键
                  joinColumns = @JoinColumn(name = "ROLE_ID"), 
               // inverseJoinColumns = @JoinColumn(name = "PRIV_ID") 表示权限多表的外键
                  inverseJoinColumns = @JoinColumn(name = "PRIV_ID")
          )
      private Set<Privilege> privileges;
      ```

   3. 在开发中最好不要使用多对多建议使用两个多对一想知道自行google

## 7 关系维护总结

```
不管是一对多、一对一还是多对多，都只需要记住一点，在哪个实体类声明了外键，就由哪个类来维护关系，在保存数据时，总是先保存的是没有维护关联关系的那一方的数据，后保存维护了关联关系的那一方的数据
```

## 8、 其他

### 8.1、 延迟和获取选项的等效注解

| Annotations                              | Lazy                   | Fetch          |
| ---------------------------------------- | ---------------------- | -------------- |
| @[One\|Many]ToOne](fetch=FetchType.LAZY) | @LazyToOne(PROXY)      | @Fetch(SELECT) |
| @[One\|Many]ToOne](fetch=FetchType.EAGER) | @LazyToOne(FALSE)      | @Fetch(JOIN)   |
| @ManyTo[One\|Many](fetch=FetchType.LAZY) | @LazyCollection(TRUE)  | @Fetch(SELECT) |
| @ManyTo[One\|Many](fetch=FetchType.EAGER) | @LazyCollection(FALSE) | @Fetch(JOIN)   |

### 8.2、Hibernate验证注解

| 注解       | 适用类型   | 说明               | 示例                         |
| -------- | ------ | ---------------- | -------------------------- |
| @Pattern | String | 通过正则表达式来验证字符串    | @Pattern(regex=”[a-z]{6}”) |
| @Length  | String | 验证字符串的长度         | @length(min=3,max=20)      |
| @Email   | String | 验证一个Email地址是否有效  | @email                     |
| @Range   | Long   | 验证一个整型是否在有效的范围内  | @Range(min=0,max=100)      |
| @Min     | Long   | 验证一个整型必须不小于指定值   | @Min(value=10)             |
| @Max     | Long   | 验证一个整型必须不大于指定值   | @Max(value=20)             |
| @Size    | 集合或数组  | 集合或数组的大小是否在指定范围内 | @Size(min=1,max=255)       |

```
@Pattern
String
通过正则表达式来验证字符串
@Pattern(regex=”^([A-Z]|[a-z]|[0-9]|[`~!@#$%^&*()+=|{}':;',\\\\[\\\\].<>/?~！@#￥%……&*（）——+|{}【】‘；：”“'。，、？]){6,20}$”)

@Length
String
验证字符串的长度
@length(min=3,max=20)

@Email
String
验证一个Email地址是否有效
@email

@Range
Long
验证一个整型是否在有效的范围内
@Range(min=0,max=100)

@Min
Long
验证一个整型必须不小于指定值
@Min(value=10)

@Max
Long
验证一个整型必须不大于指定值
@Max(value=20)

@Size
集合或数组
集合或数组的大小是否在指定范围内
@Size(min=1,max=255)
```