# JPA和Hibernate的关系

1. JPA Java Persistence API，是Java EE 5的标准ORM接口，也是ejb3规范的一部分。

2. Hibernate，是JPA的一个实现，但是其功能是JPA的超集(比JPA更强大)。

3. JPA和Hibernate之间的关系，可以简单的理解为JPA是标准接口，Hibernate是实现。那么Hibernate是如何实现与JPA的这种关系的呢。Hibernate主要是通过三个组件来实现的，及hibernate-annotation、hibernate-entitymanager和hibernate-core。

4. hibernate-annotation是Hibernate支持annotation方式配置的基础，它包括了标准的JPA annotation以及Hibernate自身特殊功能的annotation。

5. hibernate-core是Hibernate的核心实现，提供了Hibernate所有的核心功能。

6. hibernate-entitymanager实现了标准的JPA，可以把它看成hibernate-core和JPA之间的适配器，它并不直接提供ORM的功能，而是对hibernate-core进行封装，使得Hibernate符合JPA的规范。

7. 重点介绍一下hibernate-entitymanager包的主要类及实现。

   1. HibernatePersistence.java，实现了JPA的PersistenceProvider接口，它提供createEntityManagerFactory和createContainerEntityManagerFactory两个方法来创建EntityManagerFactory对象，这两个方法底层都是调用的EJB3Configuration对象的buildEntityManagerFactory方法，来解析JPA配置文件persistence.xml,，并创建EntityManagerFactory对象。
   2. EntityManagerFactory对象的实现是EntityManagerFactoryImpl类，这个类有一个最重要的private属性就是Hibernate的核心对象之一SessionFactory。这个类最重要的方法是createEntityManager，来返回EntityMnagaer对象，而sessionFactory属性也传入了该方法。
   3. EntityManager对象的实现是EntityManagerImpl类，这个类继承自AbstractEntityManagerImpl类，在AbstractEntityManager类中有一个抽象方法getSession来获得Hibernate的Session对象，正是在这个Session对象的实际支持下，EntityManagerImpl类实现了JPA的EntityManager接口的所有方法，并完成实际的ORM操作。
   4. 此外，hibernate-entitymanager包中还有QueryImpl类利用EntityManagerImpl的支持实现了JPA的Query接口；TransactionImpl利用EntityManagerImpl的支持实现了JPA的EntityTransaction接口。
   5. Hibernate通过hibernate-entitymanager包完成了对于JPA的全部支持工作。

8. 常见问题

   1. JPA中的Query对象的getSingleResult()方法，当查询不到结果时，抛出NoResultException、当查询到多个结果时，抛出NonUniqueResultException；并且NoResultException和NonUniqueResultException都是RuntimeException。

   2. 这样有两个问题：

      1、我认为getSingleResult方法应该允许查询不到结果的情况存在的，此时它返回null即可，没有必要抛出异常；

      2、即使需要在查询不到结果或者查询到多个结果时抛出异常，也不应该抛出RuntimeException，因为这样表示不需要代码显示的用try-catch块来捕获这些异常，也就不会引起用户对这两个异常的重视。





