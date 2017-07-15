Session中文API

| 返回值               | 方法说明                                     |
| ----------------- | ---------------------------------------- |
| Transaction       | beginTransaction() 开始一个工作单元并且返回相关联的事务（Transaction）对象。 |
| void              | cancelQuery() 终止执行当前查询。                  |
| void              | clear() 完整的清除这个session。                  |
| Connection        | close() 停止这个Session，通过中断JDBC连接并且清空（cleaning up）它。 |
| Connection        | connection() 获取这个Session的JDBC连接。如果这个session使用了积极的collection释放策略（如CMT-容器控制事务的环境下），关闭这个调用的连接的职责应该由当前应用程序负责。 |
| boolean           | contains(Object object) 检查这个对象实例是否与当前的Session关联（即是否为Persistent状态）。 |
| Criteria          | createCriteria(Class persistentClass) 为给定的实体类或它的超类创建一个新的Criteria实例。 |
| Criteria          | createCriteria(Class persistentClass, String alias) 根据给定的实体类或者它的超类创建一个新的Criteria实例，并赋予它（实体类）一个别名。 |
| Criteria          | createCriteria(String entityName) 根据给定的实体的名称（name），创建一个新的Criteria实例。 |
| Criteria          | createCriteria(String entityName, String alias) 根据给定的实体的名称（name），创建一个新的Criteria实例，并赋予它（实体类）一个别名 |
| Query             | createFilter(Object collection, String queryString) 根据给定的collection和过滤字符串（查询条件）创建一个新的Query实例。 |
| Query             | createQuery(String queryString) 根据给定的HQL查询条件创建一个新的Query实例。 |
| SQLQuery          | createSQLQuery(String queryString) 根据给定的SQL查询条件创建一个新的SQLQuery实例。 |
| void              | delete(Object object) 从数据库中移除持久化（persistent）对象的实例。 |
| void              | delete(String entityName, Object object) 从数据库中移除持久化（persistent）对象的实例。 |
| void              | disableFilter(String filterName) 禁用当前session的名称过滤器。 |
| Connection        | disconnect() 断开Session与当前的JDBC连接。        |
| Filter            | enableFilter(String filterName) 打开当前session的名称过滤器。 |
| void              | evict(Object object) 将当前对象实例从session缓存中清除。 |
| void              | flush() 强制提交刷新（flush）Session。            |
| Object            | get(Class clazz, Serializable id) 根据给定标识和实体类返回持久化对象的实例，如果没有符合条件的持久化对象实例则返回null。 |
| Object            | get(Class clazz, Serializable id, LockMode lockMode) 根据给定标识和实体类返回持久化对象的实例，如果没有符合条件的持久化对象实例则返回null。 |
| Object            | get(String entityName, Serializable id) 返回与给定的实体命名和标识匹配的持久化实例，如果没有对应的持久化实例则返回null。 |
| Object            | get(String entityName, Serializable id, LockMode lockMode) 返回与给定的实体类和标识所匹配的持久化实例，如果没有对应的持久化实例则返回null。 |
| CacheMode         | getCacheMode() 得到当前的缓存模式。                |
| LockMode          | getCurrentLockMode(Object object) 检测给定对象当前的锁定级别。 |
| Filter            | getEnabledFilter(String filterName) 根据名称获取一个当前允许的过滤器（filter）。 |
| EntityMode        | getEntityMode() 获取这个session有效的实体模式。      |
| String            | getEntityName(Object object) 返回一个持久化对象的实体名称。 |
| FlushMode         | getFlushMode() 获得当前的刷新提交（flush）模式。       |
| Serializable      | getIdentifier(Object object) 获取给定的实体对象实例在Session的缓存中的标识，如果该实例是自由状态（Transient）的或者与其它Session关联则抛出一个异常。 |
| Query             | getNamedQuery(String queryName) 从映射文件中根据给定的查询的名称字符串获取一个Query（查询）实例。 |
| Session           | getSession(EntityMode entityMode) 根据给定的实体模式（Entity Mode）开始一个新的有效的Session。 |
| SessionFactory    | getSessionFactory() 获取创建这个session的SessionFactory实例。 |
| SessionStatistics | getStatistics() 获取这个session的统计信息。        |
| Transaction       | getTransaction() 获取与这个session关联的Transaction（事务）实例。 instance associated with this session. |
| boolean           | isConnected() 检查当前Session是否处于连接状态。       |
| boolean           | isDirty() 当前Session是否包含需要与数据库同步的（数据状态）变化 ？如果我们刷新提交（flush）这个session是否会有SQL执行？ |
| boolean           | isOpen() 检查当前Session是否仍然打开。              |
| Object            | load(Class theClass, Serializable id) 在符合条件的实例存在的情况下，根据给定的实体类和标识返回持久化状态的实例。 |
| Object            | load(Class theClass, Serializable id, LockMode lockMode) 在符合条件的实例存在的情况下，根据给定的实体类、标识及指定的锁定等级返回持久化状态的实例。 |
| void              | load(Object object, Serializable id) 将与给定的标示对应的持久化状态（值）复制到给定的自由状态（trasient）实例上。 |
| Object            | load(String entityName, Serializable id) 在符合条件的实例存在的情况下，根据给定的实体类和标识返回持久化状态的实例。 |
| Object            | load(String entityName, Serializable id, LockMode lockMode) 在符合条件的实例存在的情况下，根据给定的实体类、标识及指定的锁定等级返回持久化状态的实例。 |
| void              | lock(Object object, LockMode lockMode) 从给定的对象上获取指定的锁定级别。 |
| void              | lock(String entityName, Object object, LockMode lockMode) 从给定的对象上获取指定的锁定级别。 |
| Object            | merge(Object object) 将给定的对象的状态复制到具有相同标识的持久化对象上。 |
| Object            | merge(String entityName, Object object) 将给定的对象的状态复制到具有相同标识的持久化对象上。 |
| void              | persist(Object object) 将一个自由状态（transient）的实例持久化。 |
| void              | persist(String entityName, Object object) 将一个自由状态（transient）的实例持久化。 |
| void              | reconnect() 不推荐的。 手工的重新连接只应用于应用程序提供连接的情况，在这种情况下或许应该使用reconnect(java.sql.Connection)。 |
| void              | reconnect(Connection connection) 重新连接到给定的JDBC连接。 |
| void              | refresh(Object object) 从数据库中重新读取给定实例的状态。 |
| void              | refresh(Object object, LockMode lockMode) 根据指定的锁定模式（LockMode），从数据库中重新读取给定实例的状态。 |
| void              | replicate(Object object, ReplicationMode replicationMode) 使用当前的标识值持久化给定的游离状态（Transient）的实体。 |
| void              | replicate(String entityName, Object object, ReplicationMode replicationMode) 使用当前的标识值持久化给定的游离状态（Transient）的实体。 |
| Serializable      | save(Object object) 首先为给定的自由状态（Transient）的对象（根据配置）生成一个标识并赋值，然后将其持久化。 |
| Serializable      | save(String entityName, Object object) 首先为给定的自由状态（Transient）的对象（根据配置）生成一个标识并赋值，然后将其持久化。 |
| void              | saveOrUpdate(Object object) 根据给定的实例的标识属性的值（注：可以指定unsaved-value。一般默认null。）来决定执行 save() 或update()操作。 |
| void              | saveOrUpdate(String entityName, Object object) 根据给定的实例的标识属性的值（注：可以指定unsaved-value。一般默认null。）来决定执行 save() 或update()操作。 |
| void              | setCacheMode(CacheMode cacheMode) 设置刷新提交模式。 |
| void              | setFlushMode(FlushMode flushMode) 设置刷新提交模式。 |
| void              | setReadOnly(Object entity, boolean readOnly) 将一个未经更改的持久化对象设置为只读模式，或者将一个只读对象标记为可以修改的模式。 |
| void              | update(Object object) 根据给定的detached（游离状态）对象实例的标识更新对应的持久化实例。 |
| void              | update(String entityName, Object object) 根据给定的detached（游离状态）对象实例的标识更新对应的持久化实例。 |