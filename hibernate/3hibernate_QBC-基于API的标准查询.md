hibernate��ѯ

- ��������ͼ������ʽ:  �����Ѿ����صĶ��󵼺�����������
- OID ������ʽ:  ���ն���� OID ����������
- HQL ������ʽ: ʹ���������� HQL ��ѯ����
- QBC ������ʽ: ʹ�� QBC(Query By Criteria) API ����������. ���� API ��װ�˻����ַ�����ʽ�Ĳ�ѯ���, �ṩ�˸����������Ĳ�ѯ�ӿ�. 
- NativeSQL������ SQL ������ʽ��ʹ�ñ������ݿ�� SQL ��ѯ���

ʹ��Query�ӿ�

NativeSQL >HQL.> EJBQL(JPQL 1.0) > QBC(Query By Criteria) > QBE(Query By Example)

# 1. QBC��Query By Criteria��QBC

����API�ı�׼��ѯ

Query query=session.createCriteria(Persion.class);

## 1.1 ������ѯ

- ʹ��query.add
- Restrictions��Property
- ��������ѯ�����query.add������ʽ����query.add().add().add()

### 1.1.1 ����һ��Restrictions&Order

- Restrictions��ľ�̬��������������ѯ

- Order��ľ�̬������������


```
query.add(Restrictions.like(��name��,��%-8%��));
query.addOrder(Order.desc(��id��));
```

### 1.1.2 ��������Property���ײ���ʵ�Ƕ�Restrictions�ķ�װ

```
query.add(Property.forName(��id��).le(5));
query.addOrder(Order.desc(��id��));
```

## 1.2 ������ѯ

ע��**������������л������ͣ�����Ĭ��ֵ��������ѯҲ��ƴĬ��ֵ�����������Խ������ʹ�÷�װ���ͣ���װ��,��Integer��Long��Double����**

```
Person p=new Person();
p.setName(��tom��);
Query query=session.createCriteria(Persion.class);
query.add(Example.create(p));
List<Person> plist=query.list();
```

## 1.3 ���߲�ѯ

���sessionʹ��ԭ������֣���дsql���ٴ�session����**�ڰ󶨲�ѯ����֮ǰ����Ҫsession**

**sessionʹ��ԭ��**��

1. ����򿪣�����رգ� ���Ծ����ͷ���Դ

2. ��Ҫ��ʱ���

3. ������Ҫ���û�����


```
DetachedCriteria dc=DetachedCriteria.forClass(Person.class);
dc.add(Restrictions.like(��name��,��%-8%��));
Criteria query=dc.getExecutableCriteria(session);
List<Person> plist=query.list();
```

# 2. HQL

## 2.1 ������

### 2.1.1 �󶨷�ʽ

- ���������֣��� ��:�� ��ͷ
- ������λ�ã��� ��?�� ���������λ��,hibernate��0��ʼ��jdbc��1��ʼ

### 2.1.2 ��ֵ��ʽ

- setXXX();

```
Query query =session.createQuery(" from Customer as c where c.name =:customerName");
query.setString("customerName",name);
//or
query.setString(0,name);
```

- setEntity(): �Ѳ�����һ���־û����

```
session.createQuery("from Order o where o.customer = :customer")
.setEntity("customer",customer).list();
```

- setParameter():���������͵Ĳ���.�÷����ĵ�����������ʽָ��Hibernateӳ������

```
setParameter("id", value)
//or
setParameter(0,value)
```

- setParameterList("ids", new Object[]{1,2,3,4,5})
- setProperties ����������һ�����������ֵ��

```
Customer c=new Customer();
c.setName("Tom"); 
c.setAge(20);
Query query =session.createQuery(" from Customer as c where c.name = : name and c.age = :age")
query.setProperties(c) ;
```

## 2.2 ��ҳ

setFirstResult() ������ʼλ��

setMaxResults() ,��ѯ�����������Query��CriteriaĬ�ϲ�ѯ����

## 2.3 ͳ�Ʋ�ѯ

- ���� ORDERBY �ؼ��ֶԲ�ѯ�������
- ���� GROUP BY �ؼ��ֶ����ݷ���
- ��HAVING �ؼ��ֶԷ��������趨Լ������
- ֧�ֵĺ�����

1. count()
2. min()
3. max()
4. sum()
5. avg()

## 2.4 ������ѯ���

- Hibernate������ӳ���ļ��ж����ַ�����ʽ�Ĳ�ѯ���. 

- \<query> Ԫ�����ڶ���һ�� HQL ��ѯ���, ���� \<class> Ԫ�ز���. 

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

- xml�����ַ���ת��

- �ڳ�����ͨ�� Session �� getNamedQuery() ������ȡ��ѯ����Ӧ�� Query ����.

  ```
  Query query=session.getNamedQuery("queryUserRanage");
  ```

## 2.5 ͶӰ��ѯ(�������Բ�ѯ)

- ���壺��ѯһ����������,����ȫ����ѯ
- ͶӰ��ѯ�����ַ�ʽ:
  1. ֱ�Ӳ飺select ���� from ����
  2. ��ѯ���ض������й��췽�������ع��췽��������� select **new Guestbook(id,name,title)** from Guestbook
  3. ��ѯ����Map��ֵ�ԣ�select **new Map**(gb.id as id,gb.name as name,gb.title as title) from Guestbook gb

## 2.6 ������ѯ

```
List students=session.createQuery("from Student s where s.classes.name like " + "'%2%'").list(); 
```

## 2.7 ���Ӳ�ѯ

������**LEFT JOIN FETCH**����ȫ��ͬ�����ݲŻ���

```
List students=session.createQuery("select s.name,c.name from Student s join s.classes c").list();  
```

������**left join��left outer join**����ߵı�����ȫ����ʾ��δƥ����ұ��ֶ�Ϊnull

```
List students=session.createQuery("select s.name,c.name from Student s left join s.classes c").list();
```

������**right join��right outer join**���ұߵı�����ȫ����ʾ��δƥ�������ֶ�Ϊnull

```
List students=session.createQuery("select s.name,c.name from Student s right join s.classes c").list(); 
```

ȫ���ӣ������������ݶ�ȫ����ʾ��δƥ����ֶ�Ϊnull

����������

**LEFT JOIN FETCH**



## 2.8 NativeSQL��ѯ

```
session.createSQLQuery("select * from test").list()
```

## 2.9 ���ƶ������ScrollableResults

ScrollableResults�ӿڰ������������ƶ��α�ķ�����

- first()ʹ�α��ƶ�����һ��
- last()ʹ�α��ƶ������һ��
- beforeFirst()ʹ�α��ƶ���������Ŀ�ͷ����һ��֮ǰ��
- afterLast()ʹ�α��ƶ����������ĩβ�����һ��֮��
- previous()ʹ�α�ӵ�ǰλ�����ϣ�����˵��ǰ���ƶ�һ��
- next()ʹ�α�ӵ�ǰλ�����£�����˵����ƶ�һ��
- scroll(int n)ʹ�α�ӵ�ǰλ���ƶ�n�У�n>0�������ƶ���n<0�������ƶ���
- setRowNumber(int n)ʹ�α��ƶ����к�Ϊn���С�����Ǵ�0��ʼ�ģ�n=-1ʱ��ʾ�ƶ������һ��
  ���Ϸ�������beforeFirst()��afterLast()����void���ͣ�����������Boolean���͡�

## 2.9 �������ݴ���

- ��������������ָ��һ�������д����������.


- һ��˵����Ӧ�þ����ܱ�����Ӧ�ò����������������Ӧ�������ݿ��ֱ�ӽ�����������������ֱ�������ݿ���ִ�������������»�ɾ����SQL��䣬��������������߼��Ƚϸ��ӣ������ͨ��ֱ�������ݿ������еĴ洢�������������������
- hibernate��ʵ�����ó���������������ʹ��jdbc api
- ���������е����ݿ�ϵͳ��֧�ִ洢���̡�����Ŀǰ��MySQL�Ͳ�֧�ִ洢���̣����Կ�����Ӧ�ò㴦��


- ��Ӧ�ò������������, ��Ҫ�����·�ʽ:

1. �Cͨ�� Session��
2. �Cͨ�� HQL 
3. �Cͨ�� StatelessSession
4. �Cͨ�� JDBC API

### 2.9.1 ͨ��Session��������

ԭ��

- Ӧ��ʱ��ղ��ڷ��ʵ����ݣ�������һ����һ�����ݺ����flush��clear����


- Hibernate �����ļ�������JDBC���������������Ŀbatch_size������ֵΪ10-50 ������֤ÿ�������ݿⷢ�͵�����SQL�������Ŀһ��
- ��������� ��identity����ʶ��������,��Hibernate�޷���JDBC����������������
- ������������ʱ, ����ر� Hibernate �Ķ�������

#### 2.9.1.1 ��������

```
for ( int i=0; i<100000; i++ ) {  
	Customer customer = new Customer(.....); 
	session.save(customer);  
	if ( i % 20 == 0 ) { //����������������ĿΪ20  
	session.flush(); //�����棬ִ����������20����¼��SQL insert��� 
	session.clear(); //��ջ����е�Customer���� 
	} 
} 
```

#### 2.9.1.2 ��������

��������������ʱ�����淽ʽ��һ�μ����������ݱ������£����ַ�ʽ����ȡ����Ϊ��

- һ����Ҫ���ص�session���棬�˷��ڴ棬
- ����N��update��䣬���ܽϲ��ʹ��ScrollableResults

```
ScrollableResults customers= session.createQuery("from Customer")
.setCacheMode(CacheMode.IGNORE) //���Եڶ�������
.scroll(ScrollMode.FORWARD_ONLY);
int count=0;
while ( customers.next() ) {
	Customer customer = (Customer) customers.get(0);
	customer.setAge(customer.getAge()+1); //����Customer�����age����
	if ( ++count % 20 == 0 ) { //����������������ĿΪ20
		session.flush();//�����棬ִ����������20����¼��SQL update���
		session.clear();//��ջ����е�Customer����
	}
}
```

### 2.9.2 ͨ��HQL��������

- ʹ��.executeUpdate()������delete����һ��ֻ��ɾ��һ������
- HQL��������ݲ��ᱻ������session�У���˲�ռ���ڴ�ռ�
- HQL ֻ֧�� INSERT INTO �� SELECT ��ʽ�Ĳ������, ����֧�� INSERT INTO �� VALUES ��ʽ�Ĳ������. ����ʹ�� HQL ���ܽ����������������

### 2.9.3 ͨ��StatelessSession��������

�Ѵ�����������Session�����л����Ĵ����ڴ�ռ䡣��Ϊһ��������������Բ�����״̬��StatelessSession�������������÷���session���ơ���֮ͬ�����ڣ�

1. StatelessSession�޻��壬ͨ�������أ����棬���µ����ݶ���������״̬
2. ����������潻��
3. ����save��update��deleteʱ����ִ��SQL
4. ���������飬����޸Ķ���������update�������ݿ�
5. ����Թ����Ķ�������������
6. ���μ���ODIΪ1�����ݣ��õ�2���ڴ��ַ��ͬ�Ķ���
7. StatelessSession�����Ĳ������Ա�Interceptor���������񵽣����ǻᱻHibernate���¼�����ϵͳ���Ե���

```
StatelessSession session = sessionFactory.openStatelessSession();
Transaction tx = session.beginTransaction();

ScrollableResults customers = session.getNamedQuery("GetCustomers")
.scroll(ScrollMode.FORWARD_ONLY);
while ( customers.next() ) {
	Customer customer = (Customer) customers.get(0);
	customer.setAge(customer.getAge()+1); //���ڴ��и���Customer�����age����;
	session.update(customer);//����ִ��update��䣬�������ݿ�����ӦCUSTOMERS��¼
}

tx.commit();
session.close();
```

### 2.9.4 ͨ��JDBC API��������������

```
Transaction tx=session.beginTransaction();
//����һ�������࣬ʵ����Work�ӿ�
Work work=new Work(){
	public void execute(Connection connection)throws SQLException{
	//ͨ��JDBC APIִ�������������µ�SQL���
	PreparedStatement stmt=connection
							.prepareStatement("update CUSTOMERS set AGE=AGE+1 "
							+"where AGE>0 ");
	stmt.executeUpdate();
	}
};
//ִ��work
session.doWork(work);
tx.commit();
```

## 2.10 fetchץȡ����

1. select��Ĭ�ϣ�N+1��SQL
2. subselect��ֻ֧��collection����Ƕ���Ӳ�ѯ��select b where a_id in (select id from a) 

```
@org.hibernate.annotations.Fetch(
org.hibernate.annotations.FetchMode.SUBSELECT
)
```

```
select i.* from ITEM i
select b.* from BID b where b.ITEM_ID in (select i.ITEM_ID from ITEM i)
```

3. join��һ��left join sql

- ��Ӱ��get,load����
- ��HQLû��Ӱ��
- ��to-one>������˵��join�ǱȽϺõı���N+1����Ĳ��ԣ���Ϊ\<many-to-one>��\<one-to-one>������ɵѿ���������
- ��һ�Զ�ʹ��joinʱ���ͻ�����ѿ��������⣬����Ҫ������@OneToManyʱʹ��
- fetch = FetchType.EAGER�ȼ���XMLӳ���е�fetch="join"

## 2.11 HQL���ڵ�����

### 2.11.1 N+1����

- ���壺���Ƿ�����N+1��sql��䡣1�����ȷ�����ѯ����id�б����䡣N������id�������в�ѯ����������в�������֮ƥ������ݣ���ô�����id������Ӧ��sql���
- ����Iterator�����N+1��SQL��䡣�����ʹ��List�����ݲ�ѯ��session����ʹ��Iterator���в�ѯ���Ͳ������N+1��SQL��䣬ԭ���ǣ�list�� Ĭ�������listÿ�ζ��ᷢ��sql��䣬list�Ὣ���ݷŵ������У��������û��棻iterate��Ĭ�������iterate���û��棬��������в����ڻ����N+1����
- �������м���ӳ��Ķ��󣬲����ʼ��϶����Ҽ��϶�������Ϊ�ӳټ��صģ�Ҳ�����N+1����

**�������**

1. ��������Ԥץȡ��������batch-size="10"�����Լ���Ϊ��n/10+1��ѯ

```
<set name="bids" inverse="true" batch-size="10">  
    <key column="ITEM_ID"/>  
    <one-to-many class="Bid"/>  
</set> 
```

```
 @org.hibernate.annotations.BatchSize(size = 10)
```



2. ץȡ����fetchʹ���Ӳ�ѯsubselect

```
<set name="bids" inverse="true" fetch="subselect">  
    <key column="ITEM_ID"/>  
    <one-to-many class="Bid"/>  
</set>  
```

3. ץȡ����fetchʹ��join���������⣺

   - ����Ϊȫ�ֵģ����ܲ������в�ѯ����Ҫ������ѯ����ӳ��


   - ���ܲ����ѿ���������

```
<set name="bids" inverse="true" fetch="join">  
    <key column="ITEM_ID"/>  
    <one-to-many class="Bid"/>  
</set>  
```

4. ʹ��HQL����Criteria��̬��ץȡ����

```
List<Item> allItems = session.createQuery("from Item i left join fetch i.bids").list();  
List<Item> allItems = session.createCriteria(Item.class).setFetchMode("bids",FetchMode.JOIN).list(); 
```

### 2.11.2�ѿ����˻�����

?	��һ���������������ϵĹ�������ʱ����ͨ��һ����ʱ���ⲿ����ץȡ����ӳ���������еļ��ϣ���ѯ����ͱ�Ȼ��һ���ѿ������ˡ�

���磺

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

hibernate������sql

```
select item.*,bid.*. image.* from ITEM item  
left outer join BID bid on item.ITEM_ID = bid.ITEM_ID  
left outer join ITEM_IMAGE image on item.ITEM_ID = image.ITEM_ID  
```

**�������**

subselect fetch��ƽ�м��ϵ��Ƽ��Ż�����