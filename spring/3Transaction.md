# 1. 事务概念

- 定义：事务就是一系列的动作, 它们被当做一个单独的工作单元. 这些动作要么全部完成, 要么全部不起作用
- 作用：用来确保数据的完整性和一致性.
- 事务的四个关键属性(ACID)
  - [ ] 原子性(atomicity)
  - [ ] 一致性(consistency)
  - [ ] 隔离性(isolation)
  - [ ] 持久性(durability)

# 2. 事务管理分类

- 编程式事务管理：将事务管理代码嵌入到业务方法中来控制事务的提交和回滚
- 声明式的事务管理：事务管理作为一种横切关注点, 可以通过 AOP 方法模块化。

Spring 通过 Spring AOP 框架支持声明式事务管理
Spring AOP是基于代理的方法, 所以只能增强公共方法. 因此, 只有公有方法才能通过 Spring AOP 进行事务管理。
Spring 既支持编程式事务管理, 也支持声明式的事务管理

# 3. 事务管理配置

## 3.1. XML事务通知方式

- <bean TransationManager 定义事务管理器，类似切面类
- \<tx:advice> 声明事务通知 关联事务管理器，类似\<aop:after method="" pointcut-ref=""/>，因定义在\<aop:config>之外，所以需要增强器使通知与切入点关联起来。
- \<aop:advisor> 增强器，定义哪些连接点使用什么什么通知，**事务通知与切点的结合**

\<aop:advisor>与\<aop:aspect>比较
1、Adivisor是一种特殊的Aspect
2、区别：advisor只持有一个Pointcut和一个advice，而aspect可以多个pointcut和多个advice

例如：事务通知tv作用于切入点bussinessService的配置

```
<tx:advice id="tv" transaction-manager="transactionManager">
<tx:attributes>
<tx:method name="save*" propagation="REQUIRED"/>
</tx:attributes>
</tx:advice>
```



```
<aop:config>
        <aop:pointcut id="bussinessService"
            expression="execution(public * x.y..*.*(..))" />
        <aop:advisor pointcut-ref="bussinessService"
            advice-ref="tv" />
</aop:config>
```



## 3.2. 注解方式

@Transactional
1、 只能标注共有方法
2、 可以标注在类上，类中所有共有方法支持事务

开启方法
启用\<tx:annotation-driven transaction-manager =””> 元素, 并为之指定事务管理器就可以了。
如果事务处理器的名称是 transactionManager, 就可以在<tx:annotation-driven> 元素中省略 transaction-manager 属性. 这个元素会自动检测该名称的事务处理器.
# 4. 事务的属性

## 4.1. 事务传播属性

当事务方法被另一个事务方法调用时, 必须指定事务应该如何传播

### 4.1.1. 传播行为分类

Spring 定义了 7 种类传播行为，常用2种

1.  required：利旧
2.  required_new：新开,调用方法的事务挂起

### 4.1.2. 配置

1)注解

@Transactional 注解的 propagation 属性中定义

```
@Transactional(propagation = Propagation.REQUIRES_NEW)
```

2)XML

```
tx:advice<tx:method propagation =””>
```

## 4.2. 事务隔离级别

**并发事务导致的问题**

1. 脏读：T1读取已被T2更新但**未提交**的数据，若T2回滚，T1的数据无效
2. 不可重复读：T1读了某字段，T2**更新了**字段，T1再次读取，值会不同
3. 幻读：T1读取数据，T2**新增或删除**数据，T1再次读取，数据记录数和第一次不一样

**解决方案**

- 理论上，为避免并发导致的问题，事务应完全隔离开，按顺序执行，但性能会很差。
- 实际上，事务以较低的隔离级别在运行
- 事务的隔离级别要得到底层数据库引擎的支持, 而不是应用程序或者框架的支持

### 4.2.1. Spring支持的隔离级别

1. Default,使用底层数据库默认的级别，大部分都是read_commited，如oracle
2. Read_uncommited,允许读取未被其他事务提交的变更，3个问题都会出现
3. Read_commited，**只允许读取已提交的变更，可避免脏读**，但另外2个问题仍会出现
4. Repeatable_read，保证对某字段多次读取相同值，事务期间禁止其他事务修改，可以避免脏读，不可重复读，但幻读仍存在
5. Serializable，保证多次读取相同的行，事务期间禁止其他事务插入，更新和删除，可以避免3个问题，但性能很差

**Oracle支持Read_commited,Serializable，Mysql都支持**

### 4.2.2. 配置

1) 注解：@Transactional 的 isolation属性

```
@Transactional(isolation = Isolation.READ_COMMITTED)
```

2) xml方式：

```
<tx:advice><tx:method Isolation=””>
```

## 4.3. 事务回滚属性

默认情况下只有未检查异常(RuntimeException和Error类型的异常)会导致事务回滚. 而受检查异常不会

### 4.3.1. 配置

1) 注解方式
@Transactional 注解的 rollbackFor 和 noRollbackFor 属性来定义，可指定多个异常类
– rollbackFor: 遇到时必须进行回滚
– noRollbackFor: 一组异常类，遇到时必须不回滚
2) XML方式

```
<tx:advice><tx:method rollback-for=”” no-rollback-for=”” >
```

## 4.4. 超时和只读属性

### 4.4.1. 配置

1) 注解方式：@Transactional 注解的readOnly timeout
2) XML方式：

```
tx:advice<tx:method read-only=”” timeout=””>
```

