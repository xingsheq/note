# 1. 整合hibernate

## 1.1 整合什么

1. IOC生成sessionFactory

2. hibernate使用spring事务

## 1.2 整合步骤

1. 先加入hibernate

2. 加入spring

3. 整合

## 1.3 配置文件参考

### 1.3.1 spring的applicationContext.xml配置

1. 导入资源文件


```
<context:property-placeholder location=”classpath:db.properties”/>
```

2. 配置数据源

```
<bean id=”dataSource” class=”com.mchange.v2.c3p0.ComboPooledDataSource”>
......
```

3. 配置SessionFactory实例: 通过Spring提供的 LocalSessionFactoryBean 进行配置

```
<bean id=”sessionFactory” class=”org.springframework.orm.hibernate4.LocalSessionFactoryBean”>
<property name=”dataSource” ref=”dataSource”></property>
<property name=”mappingLocations”
value=”classpath:com/atguigu/spring/hibernate/entities/*.hbm.xml”></property>
</bean>
```

4. 配置事务

- 事务管理器

```
<bean id=”transactionManager” class=”org.springframework.orm.hibernate4.HibernateTransactionManager”>
<property name=”sessionFactory” ref=”sessionFactory”></property>
</bean>
```

- 事务通知


```
<tx:advice id=”txAdvice” transaction-manager=”transactionManager”>
<tx:attributes>
<tx:method name=”get*” read-only=”true”/>
<tx:method name=”purchase” propagation=”REQUIRES_NEW”/>
<tx:method name=”*”/>
</tx:attributes>
</tx:advice>
```

- 事务切点

```
<aop:config>
<aop:pointcut expression=”execution(* com.atguigu.spring.hibernate.service.*.*(..))” id=”txPointcut”/>
<aop:advisor advice-ref=”txAdvice” pointcut-ref=”txPointcut”/>
</aop:config>

```

### 1.3.2 hibernate的hibernate.cfg.xml配置

该文件可以不存这，这里的配置同样可以在spring中配置，建议在这里配置，分工更清晰

```
<!– 配置 hibernate 的基本属性 –>
<!– 1. 数据源需配置到 IOC 容器中, 所以在此处不再需要配置数据源 –>
<!– 2. 关联的 .hbm.xml 也在 IOC 容器配置 SessionFactory 实例时在进行配置 –>
<!– 3. 配置 hibernate 的基本属性: 方言, SQL 显示及格式化, 生成数据表的策略以及二级缓存等. —
<property name=”hibernate.dialect”>org.hibernate.dialect.MySQL5InnoDBDialect</property>
<property name=”hibernate.show_sql”>true</property>
<property name=”hibernate.format_sql”>true</property>
<property name=”hibernate.hbm2ddl.auto”>update</property>
<!– 配置 hibernate 二级缓存相关的属性. –>
```



# 2. 整合web

## 2.1 原理

把IOC容器放到servletContext中，以便Servlet中可以获得spring的IOC容器，继而获得bean实例

### 2.1.1 代码模拟

```
public class SpringServletContextListener implements javax.servlet.ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
    //获取配置文件名称
    ServletContext servletContext=servletContextEvent.getServletContext();
    String config=servletContext.getInitParameter(“locationConfig”);
    //crate IOC
    ApplicationContext ctx=new ClassPathXmlApplicationContext(config);
    //set ctx into servletcontext
    servletContext.setAttribute(“applicationContext”,ctx);
    }
}
```

### 2.1.2 使用

public class TestServlet extends HttpServlet {
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//get ioc from servletcontext
ServletContext servletContext=getServletContext();
ApplicationContext context=(ApplicationContext)servletContext.getAttribute(“applicationContext”);
Person person=(Person)context.getBean(“person”);
person.sayHello();
}
}

## 2.2 spring配置

### 2.2.1 web.xml配置

加入如下配置即可

```
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath:applicationContext.xml</param-value>
</context-param>
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```



### 2.2.2 使用方法

jsp直接获取容器

```
<%//从application域得到IOC
ApplicationContext context= WebApplicationContextUtils.getWebApplicationContext(application);
Person person=(Person)context.getBean(Person.class);
person.sayHello();
%>
```

# 3. 整合Struts

## 3.1 整合什么

- 使用IOC管理Struts2的Action
- 原理：加入struts2-spring-plugin的jar后，Struts2的beanFactory换成新的Factory，会先到IOC容器中查找action的bean实例，如果存在则从IOC容器中取实例，如果不存在则反射生成实例。

## 3.2 整合步骤

1. 加入Struts2的jar包
2. 在spring配置文件中配置action的bean，scope必须是prototype

```
<bean id=”personAction”
class=”com.actions.PersonAction” scope=”prototype”>
<property name=”personService” ref=”personService”></property>
</bean>
```

3. 这Struts2配置文件中配置action，class配置为第二步配的bean的id

```
<action name=”person-save” class=”personAction”>
	<result>/success.jsp</result>
</action>
```

4. 加入struts2-spring-plugin的jar包

