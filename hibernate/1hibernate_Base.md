# 1. 连接配置

## 1.1 hibernate.cfg.xml配置

```
<hibernate-configuration>
<session-factory>
<property name=”hibernate.dialect”>org.hibernate.dialect.MySQL5InnoDBDialect</property>
<property name=”hibernate.connection.driver_clas”>com.mysql.jdbc.Driver</property>
<property name=”hibernate.connection.url”>jdbc:mysql:///hibernate</property>
<property name=”hibernate.connection.username”>root</property>
<property name=”hibernate.connection.password”>root</property>
<property name=”hibernate.hbm2ddl.auto”>update</property>
<property name=”hibernate.show_sql”>true</property>
<property name=”hibernate.format_sql”>true</property>
<mapping resource=”com/asb/pojo/Person.hbm.xml”/>
</session-factory>
</hibernate-configuration>
```

## 1.2 使用

**SessionFactory->Session->Transaction**，session可以进行增删改查操作

```
//通常一个程序一个SessionFactory，不用手动关闭，线程安全
SessionFactory factory=config.buildSessionFactory(
new StandardServiceRegistryBuilder().applySettings(
config.getProperties()).build());
//session是hibernate核心，增删改查都是通过session完成 非线程安全
Session session=factory.openSession();
//增删改 需要事务支持
Transaction tx=session.beginTransaction();
//todo

tx.commit();
session.close();
```

# 2. hibernate对象三种状态

- Transient(临时状态)：new出来的对象；它没有持久化，不存在于Session中——此状态中的对象为临时对象。
- Persistent(持久化状态)：已经持久化，存在于Session缓存中；如经hibernate语句save()保存的对象——此状态对象为持久对象。
- Detached(游离状态)：持久化对象脱离了Session后的对象。如Session缓存被清空后的对象。已经持久化，但不存在于Session中——此状态中的对象为游离对象。

## 2.1 三种状态的区分

（1）对象有没有Id——如果没有Id，一定是Transient状态
（2）Id在数据库中有没有
（3）在内存及session缓存中有没有

Transient：只是new出来一个对象，缓存和数据库中都没有Id；
Persistent：内存中有，缓存中有，数据库有(Id);
Detached：内存有，缓存没有，数据库有 id

## 2.2 三种状态变化

![hibernate-lifetime](C:\Users\Ly\Pictures\hibernate-lifetime.gif)



样例代码：

![hibernate-life](C:\Users\Ly\Pictures\hibernate-life.png)

# 3. 增删改查

## 3.1 增

save：返回该持久化对象的标识属性值（即主键），该方法会**立即**将持久化对象的对应数据插入数据库。
persist：**没有返回**任何值，保证当它在一个事务外部被调用时，**并不立即**转换为insert语句，适用于**长会话**流程。

区别：当对一个 OID 不为 Null 的对象执行 save() 方法时, 会把该对象以一个新的 OID 保存到数据库中;  但执行 persist() 方法时会抛出一个异常。

可设置dynamic-insert，是插入有值的字段

## 3.2 删

delete：删除持久化状态对象或删除设置了id的临时状态对象对应的持久化对象
A a=session.get();
session.delete(A);
A a=new A();
a.setId(1);
session.delete(A)–hibernate可以删掉， JPA不行

## 3.3 改

- update(A)：对象A的状态变成持久化状态，class映射可设置 select-before-update,dynamic-update(只更新变化了的字段)

- merge(A)：返回一个新的持久化对象，对象A仍是临时状态

- saveOrUpdate：id存在则更新，id未设置则新增，若设置了id，但数据库中没有，则更新失败

- 自动更新：对从数据库查询出的持久化对象，不做显式更新，当session关闭的时候会自动同步更新。**原因**：hibernate在每个session里都会做些处理，比如把查询过的对象缓存起来什么，这个时候这些对象的实例是和数据库保持关联的，hibernate会记录session生命周期内所有缓存对象的操作过程，最后都会反映到数据库去，也就是所谓的托管状态，所以才会有自动更新这种问题。

  **解决方法**：只要每次都把查询到的对象用evict（或clear）清除（记得，是每次），那么就不会有托管状态的entity，也就不会有自动更新，但这不会影响（应该）update（或saveOrUpdate）操作，evict只是清楚实例与数据库的关联而已，不是清楚实例本身。  

## 3.4 查

- get:（1）如果数据**不存在，返回null**，（2）**立即查询**
- load:（1）认为数据一定存在，如果**不存在则抛异常**，（2）如果lazy=true，则到**使用对象的时候才查询**，如果为false则和get一样
- list：立即查询
- iterate：延迟查询

list和iterate不同之处

a)	list取所有
b)	iterate先取 ID,等用到的时候再根据ID来取对象
c)	session中list第二次发出，仍会到数据库査询（原因：list只支持写入一级缓存）
d)	iterate 第二次，首先找session 级缓存

# 4. 一级缓存

- 定义：也叫session级缓存或事务级缓存，session关闭后被清理
- 原理：保存持久化对象的引用，防止被垃圾回收
- 作用：
  1. 减少开销，提高效率，例如：一个session钟多次查询一个对象，只会发起一次SQL，其他查询从缓存返回已存在的对象
  2. 合并多个操作（SQL）为一个操作（SQL）
  3. 对象循环关联时，防止死循环

## 4.1 支持缓存的方法

- get、load、iterate都支持一级缓存的读写操作，list 只支持往一级缓存中写数据，不支持读
- get、list是立即查询，load、iterate是延迟查询

- 批量插入时，分阶段清缓存，减少内存占用，如:


```
if(i%10=0){
//强制刷新,立即同步数据库，插入数据
session.flush();
//清除缓存
session.clear();
}
```

## 4.2 一级缓存管理

- evict(Object obj) 将持久化对象从一级缓存清除，变为脱管状态，成为游离对象，不影响数据库数据
- contains(Object obj) 判断对象是否在一级缓存
- session.flush()：强制刷新,立即同步到数据库，插入数据，缓存---->数据库
- session.reflush()：刷新缓存，从数据库同步，数据库---->缓存
- session.clear()：清除缓存

## 4.3 线程安全的session

SessionFactory的实现是线程安全的，Session不是线程安全的，如果多个线程同时使用一个Session实例进行CRUD，就很有可能导致数据存取的混乱

- cfg.xml配置文件中开启配置hibernate.current_session_context : thread

  ```
  <property name="current_session_context_class">thread</property>
  ```

- getCurrentSession()获得

- 需要开启事务，即使是查询也需要

- 事务不需手动关闭


# 5. 二级缓存

- 二级缓存：SessionFactory级别的缓存，可以跨越session存在。
- 一般不怎么常用
- 当Hibernate根据ID访问数据对象的时候，首先从Session一级缓存中查；查不到，如果配置了二级缓存，那么从二级缓存中查；查不到，再查询数据库，把结果按照ID放入到缓存。
- Hibernate的二级缓存策略，是针对于ID查询的缓存策略，对于条件查询则毫无作用。为此，Hibernate提供了针对条件查询的**Query Cache（查询缓存）**。


- 什么样的数据适合存放到第二级缓存中？
  1. 很少被修改的数据
  2. 不是很重要的数据，允许出现偶尔并发的数据
  3. 不会被并发访问的数据
  4. 参考数据,指的是供应用参考的常量数据，它的实例数目有限，它的实例会被许多其他类的实例引用，实例极少或者从来不会被修改。


- 不适合存放到第二级缓存的数据？
  1. 经常被修改的数据
  2. 财务数据，绝对不允许出现并发
  3. 与其他应用共享的数据。


## 5.1 开启方式

1. 需手动打开，修改hibernate.cfg.xml

```
<property name= "cache.use_second_level_cache">true</property>
<property name="cache.provider_class">org.hibernate.cache.EhCacheProvider</property>
```

2. 在实体类使用注解@Cache(usage=CacheConcurrencyStrategy.READ_WRITE)  
3. 通过id查询
4. 如果要**query**使用二级缓存，需打开查询缓存，调用Query的setCachable (true)方法指明使用二级缓存

```
session.createQuery("from Category").setCacheable(true).list();
```

