# 简介

## 一、什么是JPA

### 1、概要

> （Java Persistence API\)是SUN官方推出的Java持久化规范，它为Java开发人员提供了一种对象/关联映射工具来管理Java应用中的关系数据。它的出现主要是为了简化现有的持久化开发工作和整合ORM技术，结束现在Hibernate，TopLink，JDO等ORM框架各自为营的局面。值得注意的是，JPA是在充分吸收了现有Hibernate，TopLink，JDO 等ORM框架的基础上发展而来的，具有易于使用，伸缩性强等优点。从开发社区的反映上看，JPA手动极大的支持和赞扬，其中就包括了Spring与EJB3.0的开发团队，着眼未来几年的技术走向，JPA作为ORM领域的标准化者的目标应该不难实现

### 2、JPA包括一下三方面的技术

1. ORM映射元数据

   > JPA支持XML和JDK5.0注释两种元数据形式，元数据描述对象和表之间的映射关系，框架据此将实体对象持久化到数据库表中。

2. Java持久化API

   > 用来操作实体对象，执行CRUD操作，框架在后台替我们完成所有的事情，开发者可以从繁琐的JDBC和SQL代码中解脱出来

3. 查询语言（JPQL）

   > 这是持久化操作中很重要的一个方面，通过面向对象而非面向数据库的查询语言查询数据，避免程序的SQL语句紧密耦合。

### 3、JPA的优势

1. 标准化

   > JPA 是 JCP\(JCP（Java Community Process\) 是一个开放的国际组织，主要由Java开发者以及被授权者组成\) 组织发布的 java EE 标准之一，因此任何声称符合 JPA 标准的框架都遵循同样的架构，提供相同的访问 API，这保证了基于JPA开发的企业应用能够经过少量的修改就能够在不同的JPA框架下运行。

2. 对容器级特性的支持

   > JPA 框架中支持大数据集、事务、并发等容器级事务，这使得 JPA 超越了简单持久化框架的局限，在企业应用发挥更大的作用

3. 简单易用，集成方便

   > JPA的主要目标之一就是提供更加简单的编程模型：在JPA框架下创建实体和创建Java 类一样简单，没有任何的约束和限制，只需要使用 javax.persistence.Entity进行注释；JPA的框架和接口也都非常简单，没有太多特别的规则和设计模式的要求，开发者可以很容易的掌握。JPA基于非侵入式原则设计，因此可以很容易的和其它框架或者容器集成

4. 可媲美JDBC的查询能力

   > JPA的查询语言是面向对象而非面向数据库的，它以面向对象的自然语法构造查询语句，可以看成是Hibernate HQL的等价物。JPA定义了独特的JPQL（Java Persistence Query Language），JPQL是EJB QL的一种扩展，它是针对实体的一种查询语言，操作对象是实体，而不是关系数据库的表，而且能够支持批量更新和修改、JOIN、GROUP BY、HAVING 等通常只有 SQL 才能够提供的高级查询特性，甚至还能够支持子查询

5. 支持面向对象的高级特性

   > JPA 中能够支持面向对象的高级特性，如类之间的继承、多态和类之间的复杂关系，这样的支持能够让开发者最大限度的使用面向对象的模型设计企业应用，而不需要自行处理这些特性在关系数据库的持久化。

### 4、JPA的供应商

> JPA 的目标之一是制定一个可以由很多供应商实现的API，并且开发人员可以编码来实现该API，而不是使用私有供应商特有的API。因此开发人员只需使用供应商特有的API来获得JPA规范没有解决但应用程序中需要的功能。尽可能地使用JPA API，但是当需要供应商公开但是规范中没有提供的功能时，则使用供应商特有的API。

#### 4.1、Hibernate

> Hibernate是一个开放源代码的对象关系映射框架，它对JDBC进行了非常轻量级的对象封装，它将POJO与数据库表建立映射关系，是一个全自动的orm框架，hibernate可以自动生成SQL语句，自动执行，使得Java程序员可以随心所欲的使用对象编程思维来操纵数据库。 Hibernate可以应用在任何使用JDBC的场合，既可以在Java的客户端程序使用，也可以在Servlet/JSP的Web应用中使用，最具革命意义的是，Hibernate可以在应用EJB的J2EE架构中取代CMP，完成数据持久化的重任

#### 4.2、Spring data JPA

> \([http://projects.spring.io/spring-data-jpa/](http://projects.spring.io/spring-data-jpa/)\)  
> Spring Data JPA 是 Spring 基于 ORM 框架、JPA 规范的基础上封装的一套JPA应用框架，可使开发者用极简的代码即可实现对数据的访问和操作。它提供了包括增删改查等在内的常用功能，且易于扩展！学习并使用 Spring Data JPA 可以极大提高开发效率！

#### 4.3、 OpenJPA

> \([http://openjpa.apache.org/](http://openjpa.apache.org/)\)  
> OpenJPA 是 Apache 组织提供的开源项目，它实现了 EJB 3.0 中的 JPA 标准，为开发者提供功能强大、使用简单的持久化数据管理框架。OpenJPA 封装了和关系型数据库交互的操作，让开发者把注意力集中在编写业务逻辑上。OpenJPA 可以作为独立的持久层框架发挥作用，也可以轻松的与其它 Java EE 应用框架或者符合 EJB 3.0 标准的容器集成

#### 4.4、其它

> Apache OJB （[http://db.apache.org/ojb/）](http://db.apache.org/ojb/）)  
> Cayenne （[http://objectstyle.org/cayenne/）](http://objectstyle.org/cayenne/）)  
> Jaxor （[http://jaxor.sourceforge.NET）](http://jaxor.sourceforge.NET）)  
> jRelationalFramework （[http://ijf.sourceforge.net）](http://ijf.sourceforge.net）)  
> mirage （[http://itor.cq2.org/en/oss/mirage/toon）](http://itor.cq2.org/en/oss/mirage/toon）)  
> SMYLE （[http://www.drjava.de/smyle）](http://www.drjava.de/smyle）)  
> EclipseLink （[http://www.eclipse.org/eclipselink/）](http://www.eclipse.org/eclipselink/）)

#### 4.5、总结

> JPA规范主要关注的仅是API的行为方面，而由各种实现完成大多数性能有关的调优。尽管如此，所有可靠的实现都应该拥有某种数据缓存，以作为选择。但愿不久的将来，JPA能成为真正的标准。

## 二、什么是持久化

### 1、 说明

​    持久化（Persistence），即把数据（如内存中的对象）保存到可永久保存的存储设备中（如磁盘）。持久化的主要应用是将内存中的对象存储在关系型的数据库中，当然也可以存储在磁盘文件中、XML数据文件中等等

### 2、表现形式

1. JDBC就是一种持久化机制
2. 文件IO也是一种持久化机制

### 3、为什么要持久化

1. 内存容量有限\(内存只能用于存放临时的数据,一担关机所有的内容都会被清除\)
2. 数据管理的需要
3. 大量数据需要共享

## 三、什么是ORM

### 1、概要

1. 对象关系映射（Object Relational Mapping，简称ORM）是一种为了解决面向对象与关系数据库存在的互不匹配的现象的技术。 简单的说，ORM是通过使用描述对象和数据库之间映射的元数据，将java程序中的对象自动持久化到关系数据库中。本质上就是将数据从一种形式转换到另外一种形式。 这也同时暗示者额外的执行开销；然而，如果ORM作为一种中间件实现，则会有很多机会做优化，而这些在手写的持久层并不存在。 更重要的是用于控制转换的元数据需要提供和管理；但是同样，这些花费要比维护手写的方案要少；而且就算是遵守ODMG规范的对象数据库依然需要类级别的元数据。
2. 对象-关系映射（Object/Relation Mapping，简称ORM），是随着面向对象的软件开发方法发展而产生的。面向对象的开发方法是当今企业级应用开发环境中的主流开发方法，关系数据库是企业级应用环境中永久存放数据的主流数据存储系统。对象和关系数据是业务实体的两种表现形式，业务实体在内存中表现为对象，在数据库中表现为关系数据。内存中的对象之间存在关联和继承关系，而在数据库中，关系数据无法直接表达多对多关联和继承关系。因此，对象-关系映射\(ORM\)系统一般以中间件的形式存在，主要实现程序对象到关系数据库数据的映射。
3. 面向对象是从软件工程基本原则\(如耦合、聚合、封装\)的基础上发展起来的，而关系数据库则是从数学理论发展而来的，两套理论存在显著的区别。为了解决这个不匹配的现象,对象关系映射技术应运而生。  
4. 字母O起源于"对象"\(Object\),而R则来自于"关系"\(Relational\)。几乎所有的程序里面，都存在对象和关系数据库。在业务逻辑层和用户界面层中，我们是面向对象的。当对象信息发生变化的时候，我们需要把对象的信息保存在关系数据库中。
5. 当你开发一个应用程序的时候\(不使用O/R Mapping\),你可能会写不少数据访问层的代码，用来从数据库保存，删除，读取对象信息，等等。你在DAL中写了很多的方法来读取对象数据，改变状态对象等等任务。而这些代码写起来总是重复的

### 2、ORM组成

1. 一个对持久类对象进行CRUD操作的API
2. 一个语言或API用来规定与类和类属性相关的查询
3. 一个规定mapping metadata 的工具
4. 一种技术可以让ORM的实现同事务对象一起进行dirty checking、lazy association fetching 以及其他优化操作。

## 四、其他概念

1. JSR是Java Specification Requests的缩写，意思是Java 规范提案。是指向JCP\(Java Community Process\)提出新增一个标准化技术规范的正式请求。任何人都可以提交JSR，以向Java平台增添新的API和服务。JSR已成为Java界的一个重要标准
2. 是一个开放的国际组织，主要由Java开发者以及被授权者组成，职能是发展和更新,JCP维护的规范包括J2ME、J2SE、J2EE等。组织成员可以提交\[JSR\]（Java Specification Requests），通过特定程序以后，进入到下一版本的规范里面



