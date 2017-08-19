# 1.AOP概念

## 1.1 AOP基本概念

- AOP：面向切面编程

- Aspect:切面，横切关注点，被模块化的特殊对象，如日志处理，**可理解为通知类或切面类**

- Advice:通知，切面要完成的工作，**可理解为通知类里的各方法（before，after...）**

-  Joinpoint:连接点，程序执行的某个特定位置，如方法调用前后，异常之后。包含两部分内容

  （1）执行点，如：方法

  （2）方位：相对于执行点的位置，如：执行之前，之后

  **连接点在通知方法中做为入参**

- Pointcut:切点，连接点查询条件，匹配一个或多个连接点。连接点相当于数据库中的记录，切点相当于查询条件,使用类和方法作为连接点的查询条件。**可理解为被切入的目标方法**

- Target:被通知的对象，即目标对象

- Proxy:向目标对象应用通知后创建的对象

## 1.2 JAVA中AOP实现方式

1、 代理模式
2、 JDK动态代理实现InvocationHandler，只能代理接口
3、 Aspectj java模拟（通过反射实现），
4、 Aspectj CGLib实现（继承MethodInterceptor）：Cglib基于接口和非final类代理，不能代理static方法

## 1.3 Spring中AOP实现方式

1. ProxyFactoryBean
2. AspectJ注解
3. Spring的XML配置

# 2. AspectJ：最流行的AOP框架

## 2.1. 使用步骤

1. 加jar包:AspectJ 类库: aopalliance.jar、aspectj.weaver.jar 和 spring-aspects.jar
2. 将 aop Schema 添加到 <beans> 根元素中.
3. 在 Bean 配置文件中定义一个空的 XML 元素 <aop:aspectj-autoproxy>当 Spring IOC 容器侦测到 Bean 配置文件中的 <aop:aspectj-autoproxy> 元素时, 会自动为与 AspectJ 切面匹配的 Bean 创建代理。

## 2.2. 注解方式配置

### 2.2.1. 切面Aspect配置

@Aspect 注解的 Java 类，通知类

### 2.2.2. 通知Advice配置

AspectJ 支持 5 种类型的通知注解:

- @Before: 前置通知, 在方法执行之前执行
- @After: 后置通知, 在方法执行之后执行
- @AfterRunning: 返回通知, 在方法返回结果之后执行
- @AfterThrowing: 异常通知, 在方法抛出异常之后
- @Around: 环绕通知, 围绕着方法执行

### 2.2.3. 切入点配置

用来匹配方法的

```
execution * com.atguigu.spring.ArithmeticCalculator.*(..)
```

- 第一个 * 代表任意修饰符及任意返回值.
- 第二个 * 代表任意方法.
- .. 匹配任意数量的参数.

切入点合并
支持&&, ||, !合并多个切入点

### 2.2.4. 获得连接点信息

#### 2.2.4.1. 获得方法名和参数

通知方法中可以传入JoinPoint对象，该对象可以获得连接点信息（方法名、参数）

```
? java.lang.Object[] getArgs()：获取连接点方法运行时的入参列表，被切入的方法入参； 
? Signature getSignature() ：获取连接点的方法签名对象,被切入的方法； 
? java.lang.Object getTarget() ：获取连接点所在的目标对象，被切入的对象； 
? java.lang.Object getThis() ：获取代理对象本身； 
```

#### 2.2.4.2. 获得返回结果（返回通知）

1、 注解returning和通知方法参数中都加上相同的参数名
2、 原始的切点表达式需要出现在 pointcut 属性中

```
@AfterRunning(pointcut=””,returning=”result”)
public void logInfo(JoinPoint joinPoint,Object result){

}
```

#### 2.2.4.3. 获得异常（异常通知）

- 注解throwing和通知方法参数中都加上相同的参数名
- 原始的切点表达式需要出现在 pointcut 属性中
- 如果只对某种特殊的异常类型感兴趣, 可以将参数声明为该异常类型. 然后通知就只在抛出这个类型及其子类的异常时才被执行

定义

```
@AfterThrowing(pointcut = “”,throwing=”e”)
```

获得所有异常

```
public void doException(JoinPoint jp,Exception e)
```

获得特定异常

```
public void doException(JoinPoint jp,MyException e)
```

#### 2.2.4.4. 获得方法（环绕异常）

1、 参数是ProceedingJoinPoint它是 JoinPoint 的子接口, 允许控制何时执行, 是否执行连接点.
2、 proceed() 方法来执行被代理的方法
3、 需返回目标方法执行之后的结果, 即调用joinPoint.proceed();的返回值,否则会出现空指针异常

### 2.2.5. 切面优先级配置

- 默认先后顺序是不确定的
- 通过实现 Ordered 接口或利用 @Order 注解指定
- 返回值越小, 优先级越高.

```
@Aspect
@order(value=1)
public class Demo{

}
```

### 2.2.6. 重用切入点

@Pointcut 注解将一个切入点声明成简单的方法.
1、切入点的方法体通常是空的
2、切入点方法的访问控制符同时也控制着这个切入点的可见性，最好将它们集中在一个公共的类中，被声明为 public
3、通知可以通过方法名称引入该切入点
例：

1. 定义

   ```
   @Pointcut(“executation(* .(..))”)
   Private void getPointCut();
   ```

2. 引用

   ```
   @Befor(“getPointCut()”)

   @AfterRunning(pointcut=”getPointCut()”,returning=”result”)
   ```


## 2.3. XML配置

- XML 的配置则是Spring专有
- 正常情况下, 基于注解的声明要优先于基于 XML 的声明，因AspectJ得到越来越多的 AOP 框架支持, 所以以注解风格编写的切面将会有更多重用的机会.
- 需在\<beans> 根元素中导入 aop Schema

### 2.3.1. XML元素

#### 2.3.1.1. <aop:config>

所有aop配置都包含在里面

#### 2.3.1.2. <aop:aspect>

ref关联切面类的bean

#### 2.3.1.3. <aop:pointcut>

1、切入点必须定义在<aop:aspect>元素下, 或者直接定义在 <aop:config> 元素下
2、定义在 <aop:aspect> 元素下: 只对当前切面有效
3、定义在 <aop:config> 元素下: 对所有切面都有效
4、XML方式不支持切入点表达式中用名称引用其他切入点

#### 2.3.1.4. 通知元素

aop:after

- mothed指定切面类中通知方法的名称
- pointcut-ref 引用切入点, 或用pointcut 直接嵌入切入点表达式