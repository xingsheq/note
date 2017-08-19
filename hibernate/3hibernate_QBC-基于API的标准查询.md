hibernate查询

- 导航对象图检索方式:  根据已经加载的对象导航到其他对象
- OID 检索方式:  按照对象的 OID 来检索对象
- HQL 检索方式: 使用面向对象的 HQL 查询语言
- QBC 检索方式: 使用 QBC(Query By Criteria) API 来检索对象. 这种 API 封装了基于字符串形式的查询语句, 提供了更加面向对象的查询接口. 
- NativeSQL：本地 SQL 检索方式，使用本地数据库的 SQL 查询语句

使用Query接口

NativeSQL >HQL.> EJBQL(JPQL 1.0) > QBC(Query By Criteria) > QBE(Query By Example)

# 1. QBC（Query By Criteria）QBC

基于API的标准查询

Query query=session.createCriteria(Persion.class);

## 1.1 条件查询

- 使用query.add
- Restrictions或Property
- 多条件查询：多个query.add或者链式调用query.add().add().add()

### 1.1.1 方法一：Restrictions&Order

- Restrictions类的静态方法进行条件查询

- Order类的静态方法进行排序


```
query.add(Restrictions.like(“name”,”%-8%”));
query.addOrder(Order.desc(“id”));
```

### 1.1.2 方法二：Property，底层其实是对Restrictions的封装

```
query.add(Property.forName(“id”).le(5));
query.addOrder(Order.desc(“id”));
```

## 1.2 样例查询

注：**如果对象里面有基本类型，会有默认值，样例查询也会拼默认值的条件，所以建议对象使用封装类型（包装类,如Integer，Long，Double）。**

```
Person p=new Person();
p.setName(“tom”);
Query query=session.createCriteria(Persion.class);
query.add(Example.create(p));
List<Person> plist=query.list();
```

## 1.3 离线查询

针对session使用原则而出现，先写sql，再传session，即**在绑定查询条件之前不需要session**

**session使用原则**：

1. 最晚打开，尽早关闭， 可以尽早释放资源

2. 不要长时间打开

3. 尽量不要跨用户操作


```
DetachedCriteria dc=DetachedCriteria.forClass(Person.class);
dc.add(Restrictions.like(“name”,”%-8%”));
Criteria query=dc.getExecutableCriteria(session);
List<Person> plist=query.list();
```

# 2. HQL

## 2.1 参数绑定

### 2.1.1 绑定方式

- 按参数名字：以 “:” 开头
- 按参数位置：用 “?” 来定义参数位置,hibernate从0开始，jdbc从1开始

### 2.1.2 赋值方式

- setXXX();

```
Query query =session.createQuery(" from Customer as c where c.name =:customerName");
query.setString("customerName",name);
//or
query.setString(0,name);
```

- setEntity(): 把参数与一个持久化类绑定

```
session.createQuery("from Order o where o.customer = :customer")
.setEntity("customer",customer).list();
```

- setParameter():绑定任意类型的参数.该方法的第三个参数显式指定Hibernate映射类型

```
setParameter("id", value)
//or
setParameter(0,value)
```

- setParameterList("ids", new Object[]{1,2,3,4,5})
- setProperties 参数名称与一个对象的属性值绑定

```
Customer c=new Customer();
c.setName("Tom"); 
c.setAge(20);
Query query =session.createQuery(" from Customer as c where c.name = : name and c.age = :age")
query.setProperties(c) ;
```

## 2.2 分页

setFirstResult() 数据起始位置

setMaxResults() ,查询的最大结果集，Query和Criteria默认查询所有

## 2.3 统计查询

- 采用 ORDERBY 关键字对查询结果排序
- 利用 GROUP BY 关键字对数据分组
- 用HAVING 关键字对分组数据设定约束条件
- 支持的函数：

1. count()
2. min()
3. max()
4. sum()
5. avg()

## 2.4 命名查询语句

- Hibernate允许在映射文件中定义字符串形式的查询语句. 

- \<query> 元素用于定义一个 HQL 查询语句, 它和 \<class> 元素并列. 

  ```
  <query name="queryUserRanage">
      FROM User WHERE id BETWEEN ? AND ?
  </query>
  ```

  ```
  <query name="queryUserRanage">
      FROM User WHERE id BETWEEN :minId AND :maxId
  </query>
  ```

- xml特殊字符需转义

- 在程序中通过 Session 的 getNamedQuery() 方法获取查询语句对应的 Query 对象.

  ```
  Query query=session.getNamedQuery("queryUserRanage");
  ```

## 2.5 投影查询(部分属性查询)

- 定义：查询一个或多个属性,但不全部查询
- 投影查询有三种方式:
  1. 直接查：select 属性 from 对象
  2. 查询返回对象：须有构造方法，返回构造方法入参属性 select **new Guestbook(id,name,title)** from Guestbook
  3. 查询返回Map键值对：select **new Map**(gb.id as id,gb.name as name,gb.title as title) from Guestbook gb

## 2.6 导航查询

```
List students=session.createQuery("from Student s where s.classes.name like " + "'%2%'").list(); 
```

## 2.7 连接查询

内连接**LEFT JOIN FETCH**：完全相同的数据才会查出

```
List students=session.createQuery("select s.name,c.name from Student s join s.classes c").list();  
```

左连接**left join或left outer join**：左边的表数据全量显示，未匹配的右表字段为null

```
List students=session.createQuery("select s.name,c.name from Student s left join s.classes c").list();
```

右连接**right join或right outer join**：右边的表数据全量显示，未匹配的左表字段为null

```
List students=session.createQuery("select s.name,c.name from Student s right join s.classes c").list(); 
```

全连接：左右两边数据都全量显示，未匹配的字段为null

左迫切连接

**LEFT JOIN FETCH**



## 2.8 NativeSQL查询

```
session.createSQLQuery("select * from test").list()
```

## 2.9 可移动结果集ScrollableResults

ScrollableResults接口包含以下用于移动游标的方法：

- first()使游标移动到第一行
- last()使游标移动到最后一行
- beforeFirst()使游标移动到结果集的开头（第一行之前）
- afterLast()使游标移动到结果集的末尾（最后一行之后）
- previous()使游标从当前位置向上（或者说向前）移动一行
- next()使游标从当前位置向下（或者说向后）移动一行
- scroll(int n)使游标从当前位置移动n行（n>0，向下移动；n<0，向上移动）
- setRowNumber(int n)使游标移动到行号为n的行。编号是从0开始的，n=-1时表示移动到最后一行
  以上方法除了beforeFirst()和afterLast()返回void类型，其他都返回Boolean类型。

## 2.9 批量数据处理

- 批量处理数据是指在一个事务中处理大量数据.


- 一般说来，应该尽可能避免在应用层进行批量操作，而应该在数据库层直接进行批量操作，例如直接在数据库中执行用于批量更新或删除的SQL语句，如果批量操作的逻辑比较复杂，则可以通过直接在数据库中运行的存储过程来完成批量操作。
- hibernate其实并不擅长批量操作，建议使用jdbc api
- 并不是所有的数据库系统都支持存储过程。例如目前的MySQL就不支持存储过程，所以可能在应用层处理


- 在应用层进行批量操作, 主要有以下方式:

1. –通过 Session：
2. –通过 HQL 
3. –通过 StatelessSession
4. –通过 JDBC API

### 2.9.1 通过Session批量操作

原则：

- 应及时清空不在访问的数据，处理完一个或一批数据后调用flush，clear方法


- Hibernate 配置文件中设置JDBC单次批量处理的数目batch_size，合理值为10-50 ，程序保证每次向数据库发送的批量SQL与这个数目一致
- 若对象采用 “identity”标识符生成器,则Hibernate无法在JDBC层进行批量插入操作
- 进行批量操作时, 建议关闭 Hibernate 的二级缓存

#### 2.9.1.1 批量插入

```
for ( int i=0; i<100000; i++ ) {  
	Customer customer = new Customer(.....); 
	session.save(customer);  
	if ( i % 20 == 0 ) { //单次批量操作的数目为20  
	session.flush(); //清理缓存，执行批量插入20条记录的SQL insert语句 
	session.clear(); //清空缓存中的Customer对象 
	} 
} 
```

#### 2.9.1.2 批量更新

在批量更新数据时，常规方式是一次加载所有数据遍历更新，这种方式不可取，因为：

- 一是需要加载到session缓存，浪费内存，
- 二是N条update语句，性能较差，可使用ScrollableResults

```
ScrollableResults customers= session.createQuery("from Customer")
.setCacheMode(CacheMode.IGNORE) //忽略第二级缓存
.scroll(ScrollMode.FORWARD_ONLY);
int count=0;
while ( customers.next() ) {
	Customer customer = (Customer) customers.get(0);
	customer.setAge(customer.getAge()+1); //更新Customer对象的age属性
	if ( ++count % 20 == 0 ) { //单次批量操作的数目为20
		session.flush();//清理缓存，执行批量更新20条记录的SQL update语句
		session.clear();//清空缓存中的Customer对象
	}
}
```

### 2.9.2 通过HQL批量操作

- 使用.executeUpdate()方法，delete方法一次只能删除一条数据
- HQL处理的数据不会被保存在session中，因此不占用内存空间
- HQL 只支持 INSERT INTO … SELECT 形式的插入语句, 但不支持 INSERT INTO … VALUES 形式的插入语句. 所以使用 HQL 不能进行批量插入操作。

### 2.9.3 通过StatelessSession批量操作

把大量对象存放在Session缓存中会消耗大量内存空间。作为一种替代方案，可以采用无状态的StatelessSession来进行批量，用法与session类似。不同之处在于：

1. StatelessSession无缓冲，通过它加载，保存，更新的数据对象处于游离状态
2. 不与二级缓存交互
3. 调用save，update，delete时立即执行SQL
4. 不进行脏检查，因此修改对象后，需调用update更新数据库
5. 不会对关联的对象做级联操作
6. 两次加载ODI为1的数据，得到2个内存地址不同的对象
7. StatelessSession所做的操作可以被Interceptor拦截器捕获到，但是会被Hibernate的事件处理系统忽略掉。

```
StatelessSession session = sessionFactory.openStatelessSession();
Transaction tx = session.beginTransaction();

ScrollableResults customers = session.getNamedQuery("GetCustomers")
.scroll(ScrollMode.FORWARD_ONLY);
while ( customers.next() ) {
	Customer customer = (Customer) customers.get(0);
	customer.setAge(customer.getAge()+1); //在内存中更新Customer对象的age属性;
	session.update(customer);//立即执行update语句，更新数据库中相应CUSTOMERS记录
}

tx.commit();
session.close();
```

### 2.9.4 通过JDBC API来进行批量操作

```
Transaction tx=session.beginTransaction();
//定义一个匿名类，实现了Work接口
Work work=new Work(){
	public void execute(Connection connection)throws SQLException{
	//通过JDBC API执行用于批量更新的SQL语句
	PreparedStatement stmt=connection
							.prepareStatement("update CUSTOMERS set AGE=AGE+1 "
							+"where AGE>0 ");
	stmt.executeUpdate();
	}
};
//执行work
session.doWork(work);
tx.commit();
```

## 2.10 fetch抓取策略

1. select：默认，N+1个SQL
2. subselect：只支持collection，是嵌套子查询，select b where a_id in (select id from a) 

```
@org.hibernate.annotations.Fetch(
org.hibernate.annotations.FetchMode.SUBSELECT
)
```

```
select i.* from ITEM i
select b.* from BID b where b.ITEM_ID in (select i.ITEM_ID from ITEM i)
```

3. join：一个left join sql

- 会影响get,load方法
- 对HQL没有影响
- 对to-one>关联来说，join是比较好的避免N+1问题的策略，因为\<many-to-one>或\<one-to-one>不会造成笛卡尔积问题
- 对一对多使用join时，就会产生笛卡尔积问题，所以要避免在@OneToMany时使用
- fetch = FetchType.EAGER等价与XML映射中的fetch="join"

## 2.11 HQL存在的问题

### 2.11.1 N+1问题

- 定义：就是发出了N+1条sql语句。1：首先发出查询对象id列表的语句。N：根据id到缓存中查询，如果缓存中不存在与之匹配的数据，那么会根据id发出相应的sql语句
- 利用Iterator会出现N+1条SQL语句。如果先使用List将数据查询到session中再使用Iterator进行查询，就不会产生N+1条SQL语句，原因是：list： 默认情况下list每次都会发出sql语句，list会将数据放到缓存中，而不利用缓存；iterate：默认情况下iterate利用缓存，如果缓存中不存在会出现N+1问题
- 遍历具有集合映射的对象，并访问集合对象，且集合对象配置为延迟加载的，也会产生N+1问题

**解决方案**

1. 启用批量预抓取，如配置batch-size="10"，可以减少为：n/10+1查询

```
<set name="bids" inverse="true" batch-size="10">  
    <key column="ITEM_ID"/>  
    <one-to-many class="Bid"/>  
</set> 
```

```
 @org.hibernate.annotations.BatchSize(size = 10)
```



2. 抓取策略fetch使用子查询subselect

```
<set name="bids" inverse="true" fetch="subselect">  
    <key column="ITEM_ID"/>  
    <one-to-many class="Bid"/>  
</set>  
```

3. 抓取策略fetch使用join，存在问题：

   - 配置为全局的，可能不是所有查询都需要立即查询集合映射


   - 可能产生笛卡尔积问题

```
<set name="bids" inverse="true" fetch="join">  
    <key column="ITEM_ID"/>  
    <one-to-many class="Bid"/>  
</set>  
```

4. 使用HQL或者Criteria动态的抓取策略

```
List<Item> allItems = session.createQuery("from Item i left join fetch i.bids").list();  
List<Item> allItems = session.createCriteria(Item.class).setFetchMode("bids",FetchMode.JOIN).list(); 
```

### 2.11.2笛卡尔乘积问题

?	当一个对象有两个以上的关联集合时，且通过一个即时的外部联结抓取策略映射两个并行的集合，查询结果就必然是一个笛卡尔积了。

例如：

```
<class name="Item">  
    ...  
    <set name="bids" inverse="true" fetch="join">  
        <key column="ITEM_ID"/>  
        <one-to-many class="Bid"/>  
    </set>  
    <set name="images" fetch="join">  
        <key column="ITEM_ID"/>  
        <composite-element class="Image">  
        .....  
    </set>  
</class>  
```

hibernate发出的sql

```
select item.*,bid.*. image.* from ITEM item  
left outer join BID bid on item.ITEM_ID = bid.ITEM_ID  
left outer join ITEM_IMAGE image on item.ITEM_ID = image.ITEM_ID  
```

**解决方案**

subselect fetch是平行集合的推荐优化方案