# 1. 持久化类设计要求

/**持久化类设计要求：

1. 持久化标识符ID，**使用对象类型**（基本类型有默认值）

2. **无参构造器**（因为有些操作是通过反射进行的）

3. 持久化类**不能是final**

4. 如果使用集合类型数据，只能**使用接口类型声明**,如用List，而不能用ArrayList

5. 通常有get set方法，没有的话需设置访问策略，default-access=”field” 不建议用，默认property

  - field表示Hibernate直接读取类中的字段值，
  - property表示Hibernate通过getter和setter读取


  */

# 2. 单类映射

## 2.1 hibernate-mapping配置

<hibernate-mapping
default-access=”property” –**访问策略 **
package=”com.asb.pojo” –**包名**
default-lazy=”false”  –**延时加载**
default-cascade=”all”  –**级联操作**>

## 2.2 class配置

<!–dynamic-insert动态插入 没有赋值的属性不会参与新增操作，即sql中没有该字段–>
<!–dynamic-update动态更新 没有赋值的属性不会参与更新操作，即sql中没有该字段–>
<!–lazy 延时加载 默认true–>
<!–**mutable** 该表是否允许更新，但可以新增和删除，**用于开发时固定数据的表**–>
<!–**select-before-update** 更新前先发起查询操作，查询是否需要更新（对象是否和数据库一致），可以提高效率，但多一次查询操作，**适于大量数据更新操作**–>
<!–where 不管任何操作，都会加上where条件–>

```
<class name=”com.asb.pojo.Person”
table=”t_person”
lazy=”true”
dynamic-insert=”true”
dynamic-update=”true”
mutable=”false”
select-before-update=”false”
where=”1=1″
```

## 2.3 ID配置

主要配置自增长策略，一般为**native**

```
<id name=”id”>
<!–//自增长策略
native 根据数据库自行判断是使用identity sequence hi/lo高低算法
identity 适于内部支持标识字段的数据库（mysql sybase，dmsqb2）
sequence 序列
increment 适于int short long类型主键 每次自增1
hi/lo 通过高低算法生成–>
<generator class=”native”/>
</id>
```

## 2.4 property配置

### 2.4.1 普通属性列

<!–name对应java对象属性–>
<!–column对应数据库列名–>
<!–type数据类型–>
<!–length数据长度–>
<!–precision=”0″ 有效位数–>
<!–scale=”0″ 小数点位数–>
<!–not-null=”false” 是否允许为空–>
<!–update=”true” 是否参与更新–>
<!–insert=”true” 是否参加新增–>
<!–unique=”false” 是否唯一约束–>
<!–unique-key=”” 唯一约束名–>
<!–lazy=”false” 延时加载,如头像图片–>
<!–index=”” 指定索引–>

```
<property
name=”name”
column=”t_name”
type=”java.lang.String”
length=”64″
precision=”0″
scale=”0″
not-null=”false”
update=”true”
insert=”true”
unique=”false”
unique-key=””
lazy=”false”
index=””
/>
```

### 2.4.2 formula派生列

- 指定属性数据的来源 额外数据可以通过这个配置获取，如分页的数据总数 
- 属性需加入到类中，但不会在数据库存在列
- @Formula 是一个虚拟列，那么数据库中不需要建这一列，同样可以，如果有个列存在，hibernate也会将 其忽略。
- 如果有where子查询，那么表需要用别名
- 注解必须是在属性上的，如果有任何一个注解在方法上，那么@Formula将失效
- sql语句必须写在()中

<property name=”count”
formula=”(select count(*) from t_person)”/>

# 3.1 组件映射

- 一个对象是另外一个对象的一个部分
- Component是某个实体类的一部分，它与实体类的主要差别在于，它没有OID（Object Identifier，也就是数据中的唯一标示）
- 采用Component的目的：实现对象模型的细粒度划分，复用率高，含义明确，层次分明
- 一张表，组件不配置class
- 组件可以是Java对象（普通组件），也可以是Map（动态组件）

![component](C:\Users\xingsheq\Desktop\component.png)

![table_model](C:\Users\xingsheq\Desktop\table_model.png)

## 3.1 普通组件映射

component需要定义单独的类，实体类使用组件类为属性

```
<component name=”phone”>
	<property name=”companyPhone”/>
	<property name=”homePhone”/>
</component>
```

## 3.1 动态组件映射

实体类中使用Map属性，不需要单独的组件类

```
<!–动态组件–>
<dynamic-component name=”attribute”>
	<property name=”key1″ column=”t_key1″ type=”java.lang.String”/>
	<property name=”key2″ column=”t_key2″ type=”int”/>
	<property name=”key3″ column=”t_key3″ type=”boolean”/>
	<property name=”key4″ column=”t_key4″ type=”double”/>
</dynamic-component>

```

# 4. 集合映射

集合映射包含：array list map set bag idbag，表示**一对多**关系，需建立关联关系，会新建表。

## 4.1 array list map set

| id     | indexs | map_key | val   |
| ------ | ------ | ------- | ----- |
| 关联的表id | 数组下标   | map key | value |

- \<key>指定关联的列
- \<element>对应value的列 ，type指定hibernate的类型（string小写）
- \<list-index>数组和List使用，用于记录下标，
- \<map-key>Map使用，用于记录主键key

array list map set 性能排序：list>map>set ,**尽量用list**

<!–数组–>

```
<array name=”arrays”>
	<key column=”id”></key>
	<list-index column=”indexs”/>
	<element column=”val” type=”string”/>
</array>
```

<!–list–>

```
<list name=”myList”>
	<key column=”id”></key>
	<list-index column=”indexs”/>
	<element column=”val” type=”string”/>
</list>
```

<!–map–>

```
<map name=”myMap”>
	<key column=”id”/>
	<map-key column=”map_key” type=”string”/>
	<element column=”val” type=”string”/>
</map>
```

<!–set–>

```
<set name=”mySet”>
	<key column=”id”></key>
	<element column=”val” type=”java.lang.String”/>
</set>
```

## 4.2 bag和idbag

映射java.util.Collection

bag：可以重复存放数据，无顺序，修改删除时没有唯一id，所以不知道具体哪条，会全部删除重新插入数据
idbag：bag基础上升级，多了唯一id，但使用的是高低算法，效率低

| id    | cid      | val   |
| ----- | -------- | ----- |
| 关联的外键 | idbag的主键 | value |

<!–bag–>

```
<bag name=”mySet”>
	<key column=”id”></key>
	<element column=”val” type=”string”/>
</bag>
```

<!–idbag–>

```
<idbag name=”myList”>
	<collection-id column=”cid” type=”string”>
		<generator class=”uuid”></generator>
	</collection-id>
	<key column=”id”></key>
	<element column=”name” type=”string”/>
</idbag>
```

# 5. 关联关系映射

- 单向关联：只有一方持有引用
- 双向关联：两方都持有引用

## 5.1 一对一外键双向关联

- 从表持有主表的主键


- 先存主表，再存从表（有外键的表）否则会多执行update语句，
- 主表配置了cascade（级联）后，可只保存主表，从表会自动被保存

### 5.1.1 主表配置

使用one-to-one

- name：配置关联的对象
- cascade级联类型，配置级联cascade后，可只保存主表

cascade类型：

- [ ] all:所有操作都关联从表 save update delete
- [ ] none:不做任何操作
- [ ] sava-update:保存和更新的时候级联
- [ ] delete:只在删除时级联

```
<one-to-one name=”address” cascade=”all”/>
```

### 5.1.2 从表配置

基于外键的一对一，在从表配置many-to-one

```
<many-to-one name=”person” column=”p_id” unique=”true” not-null=”true”/>
```

- name：配置主表对象
- unique限制为true时，变为一对一关系
- column指定外键类名，不指定会默认为属性名person–>
  ?

## 5.2 一对一主键双向关联

- 从表ID与主表ID一致
- 先存主表，再存从表（有外键的表）否则会多执行update语句
- 主表配置了cascade后，可只保存主表，从表会自动被保存

### 5.2.1 主表配置

使用one-to-one

- name：配置关联的对象
- cascade级联类型，配置级联cascade后，可只保存主表

cascade类型：

- all:所有操作都关联从表 save update delete
- none:不做任何操作
- sava-update:保存和更新的时候级联
- delete:只在删除时级联

### 5.2.2 从表配置

使用one-to-one，启用约束控制constrained，id生成策略变为foreign,id与主表保持一致

```
<id name=”id”>
	<generator class=”foreign”>
		<param name=”property”>person</param>
	</generator>
</id>
```

```
<one-to-one name=”person” constrained=”true”/>
```

## 5.3 一对多外键双向关联

主表可以使用Set List Map，list会多一列记录顺序，Map会多一列记录key

### 5.3.1 主表配置

#### 5.3.1.1 set配置

```
<!–inverse=”true” 将关系控制交给多的一端来维护，因为多的一方有外键，效率更高–>
<set name=”address” cascade=”all” inverse=”true”>
	<key column=”p_id”/>
 	<!--告诉hibernate，主表类中的set集合中存放的是哪个元素-->
	<one-to-many class=”com.asb.pojo.Address”/>
</set>
```

#### 5.3.1.2 list配置

- 增加一列index记录顺序
- inverse=”false” 顺序只有Person知道，如果为true indexs将为空

```
<list name=”addressList” cascade=”all” inverse=”false”>
	<key column=”p_id”/>
	<index column=”indexs” type=”integer” />
	<one-to-many class=”com.asb.pojo.Address”/>
</list>
```

#### 5.3.1.3 map配置

- 增加一列记录key
- inverse=”false” 如果为true indexs将为空

```
<map name=”address” cascade=”all” inverse=”false”>
	<key column=”p_id”/>
	<index column=”map_key” type=”string” />
	<one-to-many class=”com.asb.pojo.Address”/>
</map>
```

### 5.3.2 从表配置

```
<many-to-one name=”person” column=”p_id”/>
```

## 5.4 多对多外键双向关联

**主从表是相对的**，配置基本一样，建立中间表维护关系

### 5.4.1 主表配置

- inverse=”true” 将关系控制交给多的一端来维护
- table：中间表名，可以不写，因为inverse=”true” 在另一端维护关系，另一端指定表名，但会生成默认的空表，建议写上表名

```
<set name=”address” cascade=”all” inverse=”true” table=”person_join_address”>
<!–//本表在中间表里的列名–>
<key column=”p_id”/>
	<many-to-many class=”com.asb.pojo.Address” column=”a_id”/>
</set>
```

### 5.4.2 从表配置

```
<set name=”persons” cascade=”all” table=”person_join_address”>
<!–//本表在中间表里的列名–>
<key column=”a_id”/>
	<many-to-many class=”com.asb.pojo.Person” column=”p_id”/>
</set>
```

# 6. 继承关系映射

配置在父类中进行

## 6.1 单表继承

- **子类数据保存在父类表**


- 用discriminator标签定义**辨别列**区分子类数据

```
<discriminator column=”type” type=”string”/>
```

type为辨别列类型：默认string,保存类名，可以是int char，如果是int，char需手动为每个类指定辨别值

```
<!--父类配置-->
<class name=”com.asb.pojo.Person” table=”t_person” discriminator-value=”0″>
	<discriminator column=”type” type=”int”/>
	<!--子类配置-->
	<subclass name=”com.asb.pojo.Student” discriminator-value=”1″>
		<property name=”snumber”/>
		<property name=”score”/>
	</subclass>
</class>
```

- 子类使用subclass标签

```
<subclass name=”com.asb.pojo.Student”>
	<property name=”snumber”/>
	<property name=”score”/>
</subclass>
```

## 6.2 具体表继承

- **子类特有属性数据保存在子表，继承的属性保存在父表**
- 使用joined-subclass标签，需指定子表名，不需要辨别列,实际是一对一主键关联
- 父类中添加joined-subclass标签配置子类，key为子表的主键，与父表id相同

```
<joined-subclass name=”com.asb.pojo.Student” table=”t_student”>
	<key column=”s_id”/>
	<property name=”snumber”/>
	<property name=”score”/>
</joined-subclass>
```

## 6.3 具体类单表继承

- **表字段包括父类字段保存在一个表**（**不建议使用**）
- 父类id生成策略改为**hilo高低算法**
- 父类中添加union-subclass标签配置子类，不再配置key

```
<id name=”id”>
	<generator class=”hilo”/>
</id>
<!--子类使用union-subclass标签配置，不再配置key-->
<union-subclass name=”com.asb.pojo.Student” table=”t_student”>
	<property name=”snumber”/>
	<property name=”score”/>
</union-subclass>
```

