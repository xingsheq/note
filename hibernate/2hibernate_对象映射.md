# 1. �־û������Ҫ��

/**�־û������Ҫ��

1. �־û���ʶ��ID��**ʹ�ö�������**������������Ĭ��ֵ��

2. **�޲ι�����**����Ϊ��Щ������ͨ��������еģ�

3. �־û���**������final**

4. ���ʹ�ü����������ݣ�ֻ��**ʹ�ýӿ���������**,����List����������ArrayList

5. ͨ����get set������û�еĻ������÷��ʲ��ԣ�default-access=��field�� �������ã�Ĭ��property

  - field��ʾHibernateֱ�Ӷ�ȡ���е��ֶ�ֵ��
  - property��ʾHibernateͨ��getter��setter��ȡ


  */

# 2. ����ӳ��

## 2.1 hibernate-mapping����

<hibernate-mapping
default-access=��property�� �C**���ʲ��� **
package=��com.asb.pojo�� �C**����**
default-lazy=��false��  �C**��ʱ����**
default-cascade=��all��  �C**��������**>

## 2.2 class����

<!�Cdynamic-insert��̬���� û�и�ֵ�����Բ������������������sql��û�и��ֶΨC>
<!�Cdynamic-update��̬���� û�и�ֵ�����Բ��������²�������sql��û�и��ֶΨC>
<!�Clazy ��ʱ���� Ĭ��true�C>
<!�C**mutable** �ñ��Ƿ�������£�������������ɾ����**���ڿ���ʱ�̶����ݵı�**�C>
<!�C**select-before-update** ����ǰ�ȷ����ѯ��������ѯ�Ƿ���Ҫ���£������Ƿ�����ݿ�һ�£����������Ч�ʣ�����һ�β�ѯ������**���ڴ������ݸ��²���**�C>
<!�Cwhere �����κβ������������where�����C>

```
<class name=��com.asb.pojo.Person��
table=��t_person��
lazy=��true��
dynamic-insert=��true��
dynamic-update=��true��
mutable=��false��
select-before-update=��false��
where=��1=1��
```

## 2.3 ID����

��Ҫ�������������ԣ�һ��Ϊ**native**

```
<id name=��id��>
<!�C//����������
native �������ݿ������ж���ʹ��identity sequence hi/lo�ߵ��㷨
identity �����ڲ�֧�ֱ�ʶ�ֶε����ݿ⣨mysql sybase��dmsqb2��
sequence ����
increment ����int short long�������� ÿ������1
hi/lo ͨ���ߵ��㷨���ɨC>
<generator class=��native��/>
</id>
```

## 2.4 property����

### 2.4.1 ��ͨ������

<!�Cname��Ӧjava�������ԨC>
<!�Ccolumn��Ӧ���ݿ������C>
<!�Ctype�������ͨC>
<!�Clength���ݳ��ȨC>
<!�Cprecision=��0�� ��Чλ���C>
<!�Cscale=��0�� С����λ���C>
<!�Cnot-null=��false�� �Ƿ�����Ϊ�ըC>
<!�Cupdate=��true�� �Ƿ������¨C>
<!�Cinsert=��true�� �Ƿ�μ������C>
<!�Cunique=��false�� �Ƿ�ΨһԼ���C>
<!�Cunique-key=���� ΨһԼ�����C>
<!�Clazy=��false�� ��ʱ����,��ͷ��ͼƬ�C>
<!�Cindex=���� ָ�������C>

```
<property
name=��name��
column=��t_name��
type=��java.lang.String��
length=��64��
precision=��0��
scale=��0��
not-null=��false��
update=��true��
insert=��true��
unique=��false��
unique-key=����
lazy=��false��
index=����
/>
```

### 2.4.2 formula������

- ָ���������ݵ���Դ �������ݿ���ͨ��������û�ȡ�����ҳ���������� 
- ��������뵽���У������������ݿ������
- @Formula ��һ�������У���ô���ݿ��в���Ҫ����һ�У�ͬ�����ԣ�����и��д��ڣ�hibernateҲ�Ὣ ����ԡ�
- �����where�Ӳ�ѯ����ô����Ҫ�ñ���
- ע��������������ϵģ�������κ�һ��ע���ڷ����ϣ���ô@Formula��ʧЧ
- sql������д��()��

<property name=��count��
formula=��(select count(*) from t_person)��/>

# 3.1 ���ӳ��

- һ������������һ�������һ������
- Component��ĳ��ʵ�����һ���֣�����ʵ�������Ҫ������ڣ���û��OID��Object Identifier��Ҳ���������е�Ψһ��ʾ��
- ����Component��Ŀ�ģ�ʵ�ֶ���ģ�͵�ϸ���Ȼ��֣������ʸߣ�������ȷ����η���
- һ�ű����������class
- ���������Java������ͨ�������Ҳ������Map����̬�����

![component](C:\Users\xingsheq\Desktop\component.png)

![table_model](C:\Users\xingsheq\Desktop\table_model.png)

## 3.1 ��ͨ���ӳ��

component��Ҫ���嵥�����࣬ʵ����ʹ�������Ϊ����

```
<component name=��phone��>
	<property name=��companyPhone��/>
	<property name=��homePhone��/>
</component>
```

## 3.1 ��̬���ӳ��

ʵ������ʹ��Map���ԣ�����Ҫ�����������

```
<!�C��̬����C>
<dynamic-component name=��attribute��>
	<property name=��key1�� column=��t_key1�� type=��java.lang.String��/>
	<property name=��key2�� column=��t_key2�� type=��int��/>
	<property name=��key3�� column=��t_key3�� type=��boolean��/>
	<property name=��key4�� column=��t_key4�� type=��double��/>
</dynamic-component>

```

# 4. ����ӳ��

����ӳ�������array list map set bag idbag����ʾ**һ�Զ�**��ϵ���轨��������ϵ�����½���

## 4.1 array list map set

| id     | indexs | map_key | val   |
| ------ | ------ | ------- | ----- |
| �����ı�id | �����±�   | map key | value |

- \<key>ָ����������
- \<element>��Ӧvalue���� ��typeָ��hibernate�����ͣ�stringСд��
- \<list-index>�����Listʹ�ã����ڼ�¼�±꣬
- \<map-key>Mapʹ�ã����ڼ�¼����key

array list map set ��������list>map>set ,**������list**

<!�C����C>

```
<array name=��arrays��>
	<key column=��id��></key>
	<list-index column=��indexs��/>
	<element column=��val�� type=��string��/>
</array>
```

<!�Clist�C>

```
<list name=��myList��>
	<key column=��id��></key>
	<list-index column=��indexs��/>
	<element column=��val�� type=��string��/>
</list>
```

<!�Cmap�C>

```
<map name=��myMap��>
	<key column=��id��/>
	<map-key column=��map_key�� type=��string��/>
	<element column=��val�� type=��string��/>
</map>
```

<!�Cset�C>

```
<set name=��mySet��>
	<key column=��id��></key>
	<element column=��val�� type=��java.lang.String��/>
</set>
```

## 4.2 bag��idbag

ӳ��java.util.Collection

bag�������ظ�������ݣ���˳���޸�ɾ��ʱû��Ψһid�����Բ�֪��������������ȫ��ɾ�����²�������
idbag��bag����������������Ψһid����ʹ�õ��Ǹߵ��㷨��Ч�ʵ�

| id    | cid      | val   |
| ----- | -------- | ----- |
| ��������� | idbag������ | value |

<!�Cbag�C>

```
<bag name=��mySet��>
	<key column=��id��></key>
	<element column=��val�� type=��string��/>
</bag>
```

<!�Cidbag�C>

```
<idbag name=��myList��>
	<collection-id column=��cid�� type=��string��>
		<generator class=��uuid��></generator>
	</collection-id>
	<key column=��id��></key>
	<element column=��name�� type=��string��/>
</idbag>
```

# 5. ������ϵӳ��

- ���������ֻ��һ����������
- ˫���������������������

## 5.1 һ��һ���˫�����

- �ӱ�������������


- �ȴ������ٴ�ӱ�������ı�������ִ��update��䣬
- ����������cascade���������󣬿�ֻ���������ӱ���Զ�������

### 5.1.1 ��������

ʹ��one-to-one

- name�����ù����Ķ���
- cascade�������ͣ����ü���cascade�󣬿�ֻ��������

cascade���ͣ�

- [ ] all:���в����������ӱ� save update delete
- [ ] none:�����κβ���
- [ ] sava-update:����͸��µ�ʱ����
- [ ] delete:ֻ��ɾ��ʱ����

```
<one-to-one name=��address�� cascade=��all��/>
```

### 5.1.2 �ӱ�����

���������һ��һ���ڴӱ�����many-to-one

```
<many-to-one name=��person�� column=��p_id�� unique=��true�� not-null=��true��/>
```

- name�������������
- unique����Ϊtrueʱ����Ϊһ��һ��ϵ
- columnָ�������������ָ����Ĭ��Ϊ������person�C>
  ?

## 5.2 һ��һ����˫�����

- �ӱ�ID������IDһ��
- �ȴ������ٴ�ӱ�������ı�������ִ��update���
- ����������cascade�󣬿�ֻ���������ӱ���Զ�������

### 5.2.1 ��������

ʹ��one-to-one

- name�����ù����Ķ���
- cascade�������ͣ����ü���cascade�󣬿�ֻ��������

cascade���ͣ�

- all:���в����������ӱ� save update delete
- none:�����κβ���
- sava-update:����͸��µ�ʱ����
- delete:ֻ��ɾ��ʱ����

### 5.2.2 �ӱ�����

ʹ��one-to-one������Լ������constrained��id���ɲ��Ա�Ϊforeign,id��������һ��

```
<id name=��id��>
	<generator class=��foreign��>
		<param name=��property��>person</param>
	</generator>
</id>
```

```
<one-to-one name=��person�� constrained=��true��/>
```

## 5.3 һ�Զ����˫�����

�������ʹ��Set List Map��list���һ�м�¼˳��Map���һ�м�¼key

### 5.3.1 ��������

#### 5.3.1.1 set����

```
<!�Cinverse=��true�� ����ϵ���ƽ������һ����ά������Ϊ���һ���������Ч�ʸ��ߨC>
<set name=��address�� cascade=��all�� inverse=��true��>
	<key column=��p_id��/>
 	<!--����hibernate���������е�set�����д�ŵ����ĸ�Ԫ��-->
	<one-to-many class=��com.asb.pojo.Address��/>
</set>
```

#### 5.3.1.2 list����

- ����һ��index��¼˳��
- inverse=��false�� ˳��ֻ��Person֪�������Ϊtrue indexs��Ϊ��

```
<list name=��addressList�� cascade=��all�� inverse=��false��>
	<key column=��p_id��/>
	<index column=��indexs�� type=��integer�� />
	<one-to-many class=��com.asb.pojo.Address��/>
</list>
```

#### 5.3.1.3 map����

- ����һ�м�¼key
- inverse=��false�� ���Ϊtrue indexs��Ϊ��

```
<map name=��address�� cascade=��all�� inverse=��false��>
	<key column=��p_id��/>
	<index column=��map_key�� type=��string�� />
	<one-to-many class=��com.asb.pojo.Address��/>
</map>
```

### 5.3.2 �ӱ�����

```
<many-to-one name=��person�� column=��p_id��/>
```

## 5.4 ��Զ����˫�����

**���ӱ�����Ե�**�����û���һ���������м��ά����ϵ

### 5.4.1 ��������

- inverse=��true�� ����ϵ���ƽ������һ����ά��
- table���м���������Բ�д����Ϊinverse=��true�� ����һ��ά����ϵ����һ��ָ����������������Ĭ�ϵĿձ�����д�ϱ���

```
<set name=��address�� cascade=��all�� inverse=��true�� table=��person_join_address��>
<!�C//�������м����������C>
<key column=��p_id��/>
	<many-to-many class=��com.asb.pojo.Address�� column=��a_id��/>
</set>
```

### 5.4.2 �ӱ�����

```
<set name=��persons�� cascade=��all�� table=��person_join_address��>
<!�C//�������м����������C>
<key column=��a_id��/>
	<many-to-many class=��com.asb.pojo.Person�� column=��p_id��/>
</set>
```

# 6. �̳й�ϵӳ��

�����ڸ����н���

## 6.1 ����̳�

- **�������ݱ����ڸ����**


- ��discriminator��ǩ����**�����**������������

```
<discriminator column=��type�� type=��string��/>
```

typeΪ��������ͣ�Ĭ��string,����������������int char�������int��char���ֶ�Ϊÿ����ָ�����ֵ

```
<!--��������-->
<class name=��com.asb.pojo.Person�� table=��t_person�� discriminator-value=��0��>
	<discriminator column=��type�� type=��int��/>
	<!--��������-->
	<subclass name=��com.asb.pojo.Student�� discriminator-value=��1��>
		<property name=��snumber��/>
		<property name=��score��/>
	</subclass>
</class>
```

- ����ʹ��subclass��ǩ

```
<subclass name=��com.asb.pojo.Student��>
	<property name=��snumber��/>
	<property name=��score��/>
</subclass>
```

## 6.2 �����̳�

- **���������������ݱ������ӱ��̳е����Ա����ڸ���**
- ʹ��joined-subclass��ǩ����ָ���ӱ���������Ҫ�����,ʵ����һ��һ��������
- ���������joined-subclass��ǩ�������࣬keyΪ�ӱ���������븸��id��ͬ

```
<joined-subclass name=��com.asb.pojo.Student�� table=��t_student��>
	<key column=��s_id��/>
	<property name=��snumber��/>
	<property name=��score��/>
</joined-subclass>
```

## 6.3 �����൥��̳�

- **���ֶΰ��������ֶα�����һ����**��**������ʹ��**��
- ����id���ɲ��Ը�Ϊ**hilo�ߵ��㷨**
- ���������union-subclass��ǩ�������࣬��������key

```
<id name=��id��>
	<generator class=��hilo��/>
</id>
<!--����ʹ��union-subclass��ǩ���ã���������key-->
<union-subclass name=��com.asb.pojo.Student�� table=��t_student��>
	<property name=��snumber��/>
	<property name=��score��/>
</union-subclass>
```

