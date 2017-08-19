# 1. ����hibernate

## 1.1 ����ʲô

1. IOC����sessionFactory

2. hibernateʹ��spring����

## 1.2 ���ϲ���

1. �ȼ���hibernate

2. ����spring

3. ����

## 1.3 �����ļ��ο�

### 1.3.1 spring��applicationContext.xml����

1. ������Դ�ļ�


```
<context:property-placeholder location=��classpath:db.properties��/>
```

2. ��������Դ

```
<bean id=��dataSource�� class=��com.mchange.v2.c3p0.ComboPooledDataSource��>
......
```

3. ����SessionFactoryʵ��: ͨ��Spring�ṩ�� LocalSessionFactoryBean ��������

```
<bean id=��sessionFactory�� class=��org.springframework.orm.hibernate4.LocalSessionFactoryBean��>
<property name=��dataSource�� ref=��dataSource��></property>
<property name=��mappingLocations��
value=��classpath:com/atguigu/spring/hibernate/entities/*.hbm.xml��></property>
</bean>
```

4. ��������

- ���������

```
<bean id=��transactionManager�� class=��org.springframework.orm.hibernate4.HibernateTransactionManager��>
<property name=��sessionFactory�� ref=��sessionFactory��></property>
</bean>
```

- ����֪ͨ


```
<tx:advice id=��txAdvice�� transaction-manager=��transactionManager��>
<tx:attributes>
<tx:method name=��get*�� read-only=��true��/>
<tx:method name=��purchase�� propagation=��REQUIRES_NEW��/>
<tx:method name=��*��/>
</tx:attributes>
</tx:advice>
```

- �����е�

```
<aop:config>
<aop:pointcut expression=��execution(* com.atguigu.spring.hibernate.service.*.*(..))�� id=��txPointcut��/>
<aop:advisor advice-ref=��txAdvice�� pointcut-ref=��txPointcut��/>
</aop:config>

```

### 1.3.2 hibernate��hibernate.cfg.xml����

���ļ����Բ����⣬���������ͬ��������spring�����ã��������������ã��ֹ�������

```
<!�C ���� hibernate �Ļ������� �C>
<!�C 1. ����Դ�����õ� IOC ������, �����ڴ˴�������Ҫ��������Դ �C>
<!�C 2. ������ .hbm.xml Ҳ�� IOC �������� SessionFactory ʵ��ʱ�ڽ������� �C>
<!�C 3. ���� hibernate �Ļ�������: ����, SQL ��ʾ����ʽ��, �������ݱ�Ĳ����Լ����������. ��
<property name=��hibernate.dialect��>org.hibernate.dialect.MySQL5InnoDBDialect</property>
<property name=��hibernate.show_sql��>true</property>
<property name=��hibernate.format_sql��>true</property>
<property name=��hibernate.hbm2ddl.auto��>update</property>
<!�C ���� hibernate ����������ص�����. �C>
```



# 2. ����web

## 2.1 ԭ��

��IOC�����ŵ�servletContext�У��Ա�Servlet�п��Ի��spring��IOC�������̶����beanʵ��

### 2.1.1 ����ģ��

```
public class SpringServletContextListener implements javax.servlet.ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
    //��ȡ�����ļ�����
    ServletContext servletContext=servletContextEvent.getServletContext();
    String config=servletContext.getInitParameter(��locationConfig��);
    //crate IOC
    ApplicationContext ctx=new ClassPathXmlApplicationContext(config);
    //set ctx into servletcontext
    servletContext.setAttribute(��applicationContext��,ctx);
    }
}
```

### 2.1.2 ʹ��

public class TestServlet extends HttpServlet {
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
//get ioc from servletcontext
ServletContext servletContext=getServletContext();
ApplicationContext context=(ApplicationContext)servletContext.getAttribute(��applicationContext��);
Person person=(Person)context.getBean(��person��);
person.sayHello();
}
}

## 2.2 spring����

### 2.2.1 web.xml����

�����������ü���

```
<context-param>
	<param-name>contextConfigLocation</param-name>
	<param-value>classpath:applicationContext.xml</param-value>
</context-param>
<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```



### 2.2.2 ʹ�÷���

jspֱ�ӻ�ȡ����

```
<%//��application��õ�IOC
ApplicationContext context= WebApplicationContextUtils.getWebApplicationContext(application);
Person person=(Person)context.getBean(Person.class);
person.sayHello();
%>
```

# 3. ����Struts

## 3.1 ����ʲô

- ʹ��IOC����Struts2��Action
- ԭ������struts2-spring-plugin��jar��Struts2��beanFactory�����µ�Factory�����ȵ�IOC�����в���action��beanʵ��������������IOC������ȡʵ���������������������ʵ����

## 3.2 ���ϲ���

1. ����Struts2��jar��
2. ��spring�����ļ�������action��bean��scope������prototype

```
<bean id=��personAction��
class=��com.actions.PersonAction�� scope=��prototype��>
<property name=��personService�� ref=��personService��></property>
</bean>
```

3. ��Struts2�����ļ�������action��class����Ϊ�ڶ������bean��id

```
<action name=��person-save�� class=��personAction��>
	<result>/success.jsp</result>
</action>
```

4. ����struts2-spring-plugin��jar��

