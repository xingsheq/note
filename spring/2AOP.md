# 1.AOP����

## 1.1 AOP��������

- AOP������������

- Aspect:���棬���й�ע�㣬��ģ�黯�������������־����**�����Ϊ֪ͨ���������**

- Advice:֪ͨ������Ҫ��ɵĹ�����**�����Ϊ֪ͨ����ĸ�������before��after...��**

-  Joinpoint:���ӵ㣬����ִ�е�ĳ���ض�λ�ã��緽������ǰ���쳣֮�󡣰�������������

  ��1��ִ�е㣬�磺����

  ��2����λ�������ִ�е��λ�ã��磺ִ��֮ǰ��֮��

  **���ӵ���֪ͨ��������Ϊ���**

- Pointcut:�е㣬���ӵ��ѯ������ƥ��һ���������ӵ㡣���ӵ��൱�����ݿ��еļ�¼���е��൱�ڲ�ѯ����,ʹ����ͷ�����Ϊ���ӵ�Ĳ�ѯ������**�����Ϊ�������Ŀ�귽��**

- Target:��֪ͨ�Ķ��󣬼�Ŀ�����

- Proxy:��Ŀ�����Ӧ��֪ͨ�󴴽��Ķ���

## 1.2 JAVA��AOPʵ�ַ�ʽ

1�� ����ģʽ
2�� JDK��̬����ʵ��InvocationHandler��ֻ�ܴ���ӿ�
3�� Aspectj javaģ�⣨ͨ������ʵ�֣���
4�� Aspectj CGLibʵ�֣��̳�MethodInterceptor����Cglib���ڽӿںͷ�final��������ܴ���static����

## 1.3 Spring��AOPʵ�ַ�ʽ

1. ProxyFactoryBean
2. AspectJע��
3. Spring��XML����

# 2. AspectJ�������е�AOP���

## 2.1. ʹ�ò���

1. ��jar��:AspectJ ���: aopalliance.jar��aspectj.weaver.jar �� spring-aspects.jar
2. �� aop Schema ��ӵ� <beans> ��Ԫ����.
3. �� Bean �����ļ��ж���һ���յ� XML Ԫ�� <aop:aspectj-autoproxy>�� Spring IOC ������⵽ Bean �����ļ��е� <aop:aspectj-autoproxy> Ԫ��ʱ, ���Զ�Ϊ�� AspectJ ����ƥ��� Bean ��������

## 2.2. ע�ⷽʽ����

### 2.2.1. ����Aspect����

@Aspect ע��� Java �֪࣬ͨ��

### 2.2.2. ֪ͨAdvice����

AspectJ ֧�� 5 �����͵�֪ͨע��:

- @Before: ǰ��֪ͨ, �ڷ���ִ��֮ǰִ��
- @After: ����֪ͨ, �ڷ���ִ��֮��ִ��
- @AfterRunning: ����֪ͨ, �ڷ������ؽ��֮��ִ��
- @AfterThrowing: �쳣֪ͨ, �ڷ����׳��쳣֮��
- @Around: ����֪ͨ, Χ���ŷ���ִ��

### 2.2.3. ���������

����ƥ�䷽����

```
execution * com.atguigu.spring.ArithmeticCalculator.*(..)
```

- ��һ�� * �����������η������ⷵ��ֵ.
- �ڶ��� * �������ⷽ��.
- .. ƥ�����������Ĳ���.

�����ϲ�
֧��&&, ||, !�ϲ���������

### 2.2.4. ������ӵ���Ϣ

#### 2.2.4.1. ��÷������Ͳ���

֪ͨ�����п��Դ���JoinPoint���󣬸ö�����Ի�����ӵ���Ϣ����������������

```
? java.lang.Object[] getArgs()����ȡ���ӵ㷽������ʱ������б�������ķ�����Σ� 
? Signature getSignature() ����ȡ���ӵ�ķ���ǩ������,������ķ����� 
? java.lang.Object getTarget() ����ȡ���ӵ����ڵ�Ŀ����󣬱�����Ķ��� 
? java.lang.Object getThis() ����ȡ��������� 
```

#### 2.2.4.2. ��÷��ؽ��������֪ͨ��

1�� ע��returning��֪ͨ���������ж�������ͬ�Ĳ�����
2�� ԭʼ���е���ʽ��Ҫ������ pointcut ������

```
@AfterRunning(pointcut=����,returning=��result��)
public void logInfo(JoinPoint joinPoint,Object result){

}
```

#### 2.2.4.3. ����쳣���쳣֪ͨ��

- ע��throwing��֪ͨ���������ж�������ͬ�Ĳ�����
- ԭʼ���е���ʽ��Ҫ������ pointcut ������
- ���ֻ��ĳ��������쳣���͸���Ȥ, ���Խ���������Ϊ���쳣����. Ȼ��֪ͨ��ֻ���׳�������ͼ���������쳣ʱ�ű�ִ��

����

```
@AfterThrowing(pointcut = ����,throwing=��e��)
```

��������쳣

```
public void doException(JoinPoint jp,Exception e)
```

����ض��쳣

```
public void doException(JoinPoint jp,MyException e)
```

#### 2.2.4.4. ��÷����������쳣��

1�� ������ProceedingJoinPoint���� JoinPoint ���ӽӿ�, ������ƺ�ʱִ��, �Ƿ�ִ�����ӵ�.
2�� proceed() ������ִ�б�����ķ���
3�� �践��Ŀ�귽��ִ��֮��Ľ��, ������joinPoint.proceed();�ķ���ֵ,�������ֿ�ָ���쳣

### 2.2.5. �������ȼ�����

- Ĭ���Ⱥ�˳���ǲ�ȷ����
- ͨ��ʵ�� Ordered �ӿڻ����� @Order ע��ָ��
- ����ֵԽС, ���ȼ�Խ��.

```
@Aspect
@order(value=1)
public class Demo{

}
```

### 2.2.6. ���������

@Pointcut ע�⽫һ������������ɼ򵥵ķ���.
1�������ķ�����ͨ���ǿյ�
2������㷽���ķ��ʿ��Ʒ�ͬʱҲ��������������Ŀɼ��ԣ���ý����Ǽ�����һ�����������У�������Ϊ public
3��֪ͨ����ͨ��������������������
����

1. ����

   ```
   @Pointcut(��executation(* .(..))��)
   Private void getPointCut();
   ```

2. ����

   ```
   @Befor(��getPointCut()��)

   @AfterRunning(pointcut=��getPointCut()��,returning=��result��)
   ```


## 2.3. XML����

- XML ����������Springר��
- ���������, ����ע�������Ҫ�����ڻ��� XML ����������AspectJ�õ�Խ��Խ��� AOP ���֧��, ������ע�����д�����潫���и������õĻ���.
- ����\<beans> ��Ԫ���е��� aop Schema

### 2.3.1. XMLԪ��

#### 2.3.1.1. <aop:config>

����aop���ö�����������

#### 2.3.1.2. <aop:aspect>

ref�����������bean

#### 2.3.1.3. <aop:pointcut>

1���������붨����<aop:aspect>Ԫ����, ����ֱ�Ӷ����� <aop:config> Ԫ����
2�������� <aop:aspect> Ԫ����: ֻ�Ե�ǰ������Ч
3�������� <aop:config> Ԫ����: ���������涼��Ч
4��XML��ʽ��֧���������ʽ���������������������

#### 2.3.1.4. ֪ͨԪ��

aop:after

- mothedָ����������֪ͨ����������
- pointcut-ref ���������, ����pointcut ֱ��Ƕ���������ʽ