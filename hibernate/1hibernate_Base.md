# 1. ��������

## 1.1 hibernate.cfg.xml����

```
<hibernate-configuration>
<session-factory>
<property name=��hibernate.dialect��>org.hibernate.dialect.MySQL5InnoDBDialect</property>
<property name=��hibernate.connection.driver_clas��>com.mysql.jdbc.Driver</property>
<property name=��hibernate.connection.url��>jdbc:mysql:///hibernate</property>
<property name=��hibernate.connection.username��>root</property>
<property name=��hibernate.connection.password��>root</property>
<property name=��hibernate.hbm2ddl.auto��>update</property>
<property name=��hibernate.show_sql��>true</property>
<property name=��hibernate.format_sql��>true</property>
<mapping resource=��com/asb/pojo/Person.hbm.xml��/>
</session-factory>
</hibernate-configuration>
```

## 1.2 ʹ��

**SessionFactory->Session->Transaction**��session���Խ�����ɾ�Ĳ����

```
//ͨ��һ������һ��SessionFactory�������ֶ��رգ��̰߳�ȫ
SessionFactory factory=config.buildSessionFactory(
new StandardServiceRegistryBuilder().applySettings(
config.getProperties()).build());
//session��hibernate���ģ���ɾ�Ĳ鶼��ͨ��session��� ���̰߳�ȫ
Session session=factory.openSession();
//��ɾ�� ��Ҫ����֧��
Transaction tx=session.beginTransaction();
//todo

tx.commit();
session.close();
```

# 2. hibernate��������״̬

- Transient(��ʱ״̬)��new�����Ķ�����û�г־û�����������Session�С�����״̬�еĶ���Ϊ��ʱ����
- Persistent(�־û�״̬)���Ѿ��־û���������Session�����У��羭hibernate���save()����Ķ��󡪡���״̬����Ϊ�־ö���
- Detached(����״̬)���־û�����������Session��Ķ�����Session���汻��պ�Ķ����Ѿ��־û�������������Session�С�����״̬�еĶ���Ϊ�������

## 2.1 ����״̬������

��1��������û��Id�������û��Id��һ����Transient״̬
��2��Id�����ݿ�����û��
��3�����ڴ漰session��������û��

Transient��ֻ��new����һ�����󣬻�������ݿ��ж�û��Id��
Persistent���ڴ����У��������У����ݿ���(Id);
Detached���ڴ��У�����û�У����ݿ��� id

## 2.2 ����״̬�仯

![hibernate-lifetime](C:\Users\Ly\Pictures\hibernate-lifetime.gif)



�������룺

![hibernate-life](C:\Users\Ly\Pictures\hibernate-life.png)

# 3. ��ɾ�Ĳ�

## 3.1 ��

save�����ظó־û�����ı�ʶ����ֵ�������������÷�����**����**���־û�����Ķ�Ӧ���ݲ������ݿ⡣
persist��**û�з���**�κ�ֵ����֤������һ�������ⲿ������ʱ��**��������**ת��Ϊinsert��䣬������**���Ự**���̡�

���𣺵���һ�� OID ��Ϊ Null �Ķ���ִ�� save() ����ʱ, ��Ѹö�����һ���µ� OID ���浽���ݿ���;  ��ִ�� persist() ����ʱ���׳�һ���쳣��

������dynamic-insert���ǲ�����ֵ���ֶ�

## 3.2 ɾ

delete��ɾ���־û�״̬�����ɾ��������id����ʱ״̬�����Ӧ�ĳ־û�����
A a=session.get();
session.delete(A);
A a=new A();
a.setId(1);
session.delete(A)�Chibernate����ɾ���� JPA����

## 3.3 ��

- update(A)������A��״̬��ɳ־û�״̬��classӳ������� select-before-update,dynamic-update(ֻ���±仯�˵��ֶ�)

- merge(A)������һ���µĳ־û����󣬶���A������ʱ״̬

- saveOrUpdate��id��������£�idδ��������������������id�������ݿ���û�У������ʧ��

- �Զ����£��Դ����ݿ��ѯ���ĳ־û����󣬲�����ʽ���£���session�رյ�ʱ����Զ�ͬ�����¡�**ԭ��**��hibernate��ÿ��session�ﶼ����Щ��������Ѳ�ѯ���Ķ��󻺴�����ʲô�����ʱ����Щ�����ʵ���Ǻ����ݿⱣ�ֹ����ģ�hibernate���¼session�������������л������Ĳ������̣���󶼻ᷴӳ�����ݿ�ȥ��Ҳ������ν���й�״̬�����ԲŻ����Զ������������⡣

  **�������**��ֻҪÿ�ζ��Ѳ�ѯ���Ķ�����evict����clear��������ǵã���ÿ�Σ�����ô�Ͳ������й�״̬��entity��Ҳ�Ͳ������Զ����£����ⲻ��Ӱ�죨Ӧ�ã�update����saveOrUpdate��������evictֻ�����ʵ�������ݿ�Ĺ������ѣ��������ʵ������  

## 3.4 ��

- get:��1���������**�����ڣ�����null**����2��**������ѯ**
- load:��1����Ϊ����һ�����ڣ����**�����������쳣**����2�����lazy=true����**ʹ�ö����ʱ��Ų�ѯ**�����Ϊfalse���getһ��
- list��������ѯ
- iterate���ӳٲ�ѯ

list��iterate��֮ͬ��

a)	listȡ����
b)	iterate��ȡ ID,���õ���ʱ���ٸ���ID��ȡ����
c)	session��list�ڶ��η������Իᵽ���ݿ��ѯ��ԭ��listֻ֧��д��һ�����棩
d)	iterate �ڶ��Σ�������session ������

# 4. һ������

- ���壺Ҳ��session����������񼶻��棬session�رպ�����
- ԭ������־û���������ã���ֹ����������
- ���ã�
  1. ���ٿ��������Ч�ʣ����磺һ��session�Ӷ�β�ѯһ������ֻ�ᷢ��һ��SQL��������ѯ�ӻ��淵���Ѵ��ڵĶ���
  2. �ϲ����������SQL��Ϊһ��������SQL��
  3. ����ѭ������ʱ����ֹ��ѭ��

## 4.1 ֧�ֻ���ķ���

- get��load��iterate��֧��һ������Ķ�д������list ֻ֧����һ��������д���ݣ���֧�ֶ�
- get��list��������ѯ��load��iterate���ӳٲ�ѯ

- ��������ʱ���ֽ׶��建�棬�����ڴ�ռ�ã���:


```
if(i%10=0){
//ǿ��ˢ��,����ͬ�����ݿ⣬��������
session.flush();
//�������
session.clear();
}
```

## 4.2 һ���������

- evict(Object obj) ���־û������һ�������������Ϊ�ѹ�״̬����Ϊ������󣬲�Ӱ�����ݿ�����
- contains(Object obj) �ж϶����Ƿ���һ������
- session.flush()��ǿ��ˢ��,����ͬ�������ݿ⣬�������ݣ�����---->���ݿ�
- session.reflush()��ˢ�»��棬�����ݿ�ͬ�������ݿ�---->����
- session.clear()���������

## 4.3 �̰߳�ȫ��session

SessionFactory��ʵ�����̰߳�ȫ�ģ�Session�����̰߳�ȫ�ģ��������߳�ͬʱʹ��һ��Sessionʵ������CRUD���ͺ��п��ܵ������ݴ�ȡ�Ļ���

- cfg.xml�����ļ��п�������hibernate.current_session_context : thread

  ```
  <property name="current_session_context_class">thread</property>
  ```

- getCurrentSession()���

- ��Ҫ�������񣬼�ʹ�ǲ�ѯҲ��Ҫ

- �������ֶ��ر�


# 5. ��������

- �������棺SessionFactory����Ļ��棬���Կ�Խsession���ڡ�
- һ�㲻��ô����
- ��Hibernate����ID�������ݶ����ʱ�����ȴ�Sessionһ�������в飻�鲻������������˶������棬��ô�Ӷ��������в飻�鲻�����ٲ�ѯ���ݿ⣬�ѽ������ID���뵽���档
- Hibernate�Ķ���������ԣ��������ID��ѯ�Ļ�����ԣ�����������ѯ��������á�Ϊ�ˣ�Hibernate�ṩ�����������ѯ��**Query Cache����ѯ���棩**��


- ʲô���������ʺϴ�ŵ��ڶ��������У�
  1. ���ٱ��޸ĵ�����
  2. ���Ǻ���Ҫ�����ݣ��������ż������������
  3. ���ᱻ�������ʵ�����
  4. �ο�����,ָ���ǹ�Ӧ�òο��ĳ������ݣ�����ʵ����Ŀ���ޣ�����ʵ���ᱻ����������ʵ�����ã�ʵ�����ٻ��ߴ������ᱻ�޸ġ�


- ���ʺϴ�ŵ��ڶ�����������ݣ�
  1. �������޸ĵ�����
  2. �������ݣ����Բ�������ֲ���
  3. ������Ӧ�ù�������ݡ�


## 5.1 ������ʽ

1. ���ֶ��򿪣��޸�hibernate.cfg.xml

```
<property name= "cache.use_second_level_cache">true</property>
<property name="cache.provider_class">org.hibernate.cache.EhCacheProvider</property>
```

2. ��ʵ����ʹ��ע��@Cache(usage=CacheConcurrencyStrategy.READ_WRITE)  
3. ͨ��id��ѯ
4. ���Ҫ**query**ʹ�ö������棬��򿪲�ѯ���棬����Query��setCachable (true)����ָ��ʹ�ö�������

```
session.createQuery("from Category").setCacheable(true).list();
```

