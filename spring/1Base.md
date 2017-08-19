# 1. 获得容器 #
ApplicationContext context=new ClassPathXmlApplicationContext(“spring-config.xml”);
# 2. getBean方式 #
1. 通过唯一id
2. 通过Class类型

# 3. bean生成方式 #
1. 反射（全类名）
2. 工厂方法（静态工厂方法和实例工厂方法）
- 静态：需要在 Bean 的 class 属性里指定拥有该工厂的方法的类, 同时在 factory-method 属性里指定工厂方法的名称. 最后, 使用 <constrctor-arg> 元素为该方法传递方法参数
- 实例：在bean的 factory-bean属性里指定拥有该工厂方法的 Bean 在 factory-method 属性里指定该工厂方法的名称 使用 construtor-arg 元素为工厂方法传递方法参数
3. FactoryBean

# 4. XML配置 #
## 4.1. 基本类型

- 略...
- value 特殊字符使用<![CDATA[]]>

## 4.2. 赋值

- 空值赋值：使用<null/>赋空值

- 级联赋值：car.maxSpeed

- 集合赋值：（1）list节点 （2）map+entry （3）props

  单例的集合bean,可以被多个其他bean共享 需导入util命名空间

  ```
  <util:list id=”cars”>

  <ref bean=”car”/>

  <ref bean=”car2″/>

  </util:list>
  ```

  ​

- 属性赋值：使用property或p命名空间 为bean属性赋值 需导入p命名空间

  ```
  <bean id=”person5″ class=”com.asb.spring.Person” p:name=”TOM”></bean>
  ```

## 4.3. 引用

ref（bean之间：分外部，内部bean（例如car））

```
<bean id=”person2″ class=”com.asb.spring.Person”>

<property name=”name” value=”Tom”/>

<property name=”car”>

<bean class=”com.asb.spring.Car”>

<constructor-arg value=”Audi”></constructor-arg>

<constructor-arg value=”Shanghai”></constructor-arg>

<constructor-arg value=”30″></constructor-arg>

<constructor-arg value=”180″></constructor-arg>

</bean>

</property>

</bean>
```

## 4.4. 依赖注入方式

- 属性注入：set方法 配置property
- 构造方法注入：构造函数 配置constructor-arg
- 工厂方法注入：（不常用）

## 4.5. bean之间关系

### 4.5.1. 继承Parent

```
<bean id=”carnetMgr” parent=”txProxyTemplate”>
```

- 父bean可用abstract标记为模板后不能被实例化，
- 若bean没有class属性，则必须为抽象bean
- 并不是 <bean> 元素里的所有属性都会被继承. 比如: autowire, abstract 等.

### 4.5.2. 依赖depends-on

使用后，必须指定依赖的属性的bean，属前置依赖，先初始依赖的bean，多个用逗号隔开

## 4.6. bean的作用域

（1）singleton：默认单例 scope=singleton 在创建容器时创建对象
（2）prototype：非单例，在getBean时才创建对象，每次创建一个新的对象
（3）required：仅适于WebApplicationContext环境
（4）Session: 仅适于WebApplicationContext环境

# 5. 注解方式配置bean

## 5.1. 注解标签

@Component: 基本注解, 标识了一个受 Spring 管理的组件
@Respository: 标识持久层组件
@Service: 标识服务层(业务层)组件
@Controller: 标识表现层组件

## 5.2. 组件扫描

```
context:component-scan
```

- <context:include-filter> 子节点表示要包含的目标类
- <context:exclude-filter> 子节点表示要排除在外的目标类
- <context:component-scan> 下可以拥有若干个 <context:include-filter> 和 <context:exclude-filter> 子节点

## 5.3. 自动装配（建议使用@Autowired）

<context:component-scan> 元素还会自动注册 AutowiredAnnotationBeanPostProcessor实例, 该实例可以自动装配具有 @Autowired 和 @Resource 、@Inject注解的属性.

### 5.3.1. 注解名

#### 5.3.1.1. @Autowired

（1）要求有且只有一个bean，如果没有则报错，可以设置required = false，当找不到对应的bean时，也不报错@Autowired(required = false)
（2）当IOC 容器里存在多个类型兼容的Bean时, 通过类型的自动装配将无法工作. 此时可以在 @Qualifier注解里提供Bean的名称. Spring允许对方法的入参标注 @Qualifiter 已指定注入 Bean 的名称

#### 5.3.1.2. @Resource

注解要求提供一个Bean名称的属性，若该属性为空，则自动采用标注处的变量或方法名作为Bean的名称

#### 5.3.1.3. @Inject

和 @Autowired 注解一样也是按类型匹配注入的 Bean， 但没有reqired属性

### 5.3.2. 装配方式

（1）byName 根据bean名字和属性名自动装配（@Resource）
（2）byType 根据bean类型和属性类型自动装配，同类型bean只能有一个(@Autowired @Inject)

# 6. 使用外部属性文件

原理：BeanFactory后置处理器PropertyPlaceholderConfigurer
配置：context命名空间 导入配置文件
<context:property-placeholder location=”classpath:xx.properties”>
使用：${var} 引用properties中的配置项

# 7. 整合多个配置文件

- Spring 允许通过 <import> 将多个配置文件引入到一个文件中，进行配置文件的集成。这样在启动 Spring 容器时，仅需要指定这个合并好的配置文件就可以。
- import 元素的 resource 属性支持 Spring 的标准的路径资源

# 8. SpEl（spring表达式语言）

spring表达式语言 支持运算符，比较，逻辑 正则，if else
作用：动态赋值 #{}

- 引用其他对象 替换ref value=”#{beanid}”
- 引用其他对象的属性value=”#{beanid.属性}”
- 调用其他方法，可以链式操作value=”#{beanid.toString()}”
- 调用静态方法或属性value=”#{T(java.lang.Math).PI}” T()返回一个ClassObject

# 9. bean的生命周期

## 9.1. 生命周期过程

1.  constructor–通过构造器或工厂方法创建 Bean 实例

2. set–为 Bean 的属性设置值和对其他 Bean 的引用

3. 将 Bean 实例传递给 Bean 后置处理器的postProcessBeforeInitialization方法

4. init –调用Bean的初始化方法

   将 Bean 实例传递给Bean后置处理器的postProcessAfterInitialization方法

5. finish — Bean 可以使用了

6. ctx.close()的时候destroy–-当容器关闭时, 调用Bean的销毁方法

## 9.2. 管理生命周期

### 9.2.1. 指定初始化和销毁方法

- XML中配置init-mothed初始化后执行，
- XML中配置destroy-mothed销毁前执行

也可以理解为：为Bean指定初始化和销毁方法

### 9.2.2. bean后置处理器

定义：在调用初始化方法前后对bean额外处理，处理所有bean，需实现BeanPostProcessor接口。

- 有2个方法 after和before都返回实例化对象，可以在方法里面修改bean
- 配置bean后置处理器不需要配置id 会自动识别

# 10. 泛型依赖注入

BaseRepository.java

```
public class BaseRepository<T> {

}
```

BaseService.java

```
public class BaseService <T>{

@Autowired

protected BaseRepository<T> repository;

public void add(){

System.out.println(“add…”);

System.out.println(repository);

}

}
```

UserRepository.java

```
@Repository

public class UserRepository extends BaseRepository<User>{

}
```

UserService.java

```
@Service

public class UserService extends BaseService<User>{

}
```

Main.java

```
public class Main {

public static void main(String[] args) {

ApplicationContext ctx = new ClassPathXmlApplicationContext(“beans-generic-di.xml”);

UserService userService = (UserService)ctx.getBean(“userService”);

userService.add();

}

}
```


