# 1. �������

- ���壺�������һϵ�еĶ���, ���Ǳ�����һ�������Ĺ�����Ԫ. ��Щ����Ҫôȫ�����, Ҫôȫ����������
- ���ã�����ȷ�����ݵ������Ժ�һ����.
- ������ĸ��ؼ�����(ACID)
  - [ ] ԭ����(atomicity)
  - [ ] һ����(consistency)
  - [ ] ������(isolation)
  - [ ] �־���(durability)

# 2. ����������

- ���ʽ�������������������Ƕ�뵽ҵ�񷽷���������������ύ�ͻع�
- ����ʽ������������������Ϊһ�ֺ��й�ע��, ����ͨ�� AOP ����ģ�黯��

Spring ͨ�� Spring AOP ���֧������ʽ�������
Spring AOP�ǻ��ڴ���ķ���, ����ֻ����ǿ��������. ���, ֻ�й��з�������ͨ�� Spring AOP �����������
Spring ��֧�ֱ��ʽ�������, Ҳ֧������ʽ���������

# 3. �����������

## 3.1. XML����֪ͨ��ʽ

- <bean TransationManager �������������������������
- \<tx:advice> ��������֪ͨ �������������������\<aop:after method="" pointcut-ref=""/>��������\<aop:config>֮�⣬������Ҫ��ǿ��ʹ֪ͨ����������������
- \<aop:advisor> ��ǿ����������Щ���ӵ�ʹ��ʲôʲô֪ͨ��**����֪ͨ���е�Ľ��**

\<aop:advisor>��\<aop:aspect>�Ƚ�
1��Adivisor��һ�������Aspect
2������advisorֻ����һ��Pointcut��һ��advice����aspect���Զ��pointcut�Ͷ��advice

���磺����֪ͨtv�����������bussinessService������

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



## 3.2. ע�ⷽʽ

@Transactional
1�� ֻ�ܱ�ע���з���
2�� ���Ա�ע�����ϣ��������й��з���֧������

��������
����\<tx:annotation-driven transaction-manager =����> Ԫ��, ��Ϊָ֮������������Ϳ����ˡ�
������������������� transactionManager, �Ϳ�����<tx:annotation-driven> Ԫ����ʡ�� transaction-manager ����. ���Ԫ�ػ��Զ��������Ƶ���������.
# 4. ���������

## 4.1. ���񴫲�����

�����񷽷�����һ�����񷽷�����ʱ, ����ָ������Ӧ����δ���

### 4.1.1. ������Ϊ����

Spring ������ 7 ���ഫ����Ϊ������2��

1.  required������
2.  required_new���¿�,���÷������������

### 4.1.2. ����

1)ע��

@Transactional ע��� propagation �����ж���

```
@Transactional(propagation = Propagation.REQUIRES_NEW)
```

2)XML

```
tx:advice<tx:method propagation =����>
```

## 4.2. ������뼶��

**���������µ�����**

1. �����T1��ȡ�ѱ�T2���µ�**δ�ύ**�����ݣ���T2�ع���T1��������Ч
2. �����ظ�����T1����ĳ�ֶΣ�T2**������**�ֶΣ�T1�ٴζ�ȡ��ֵ�᲻ͬ
3. �ö���T1��ȡ���ݣ�T2**������ɾ��**���ݣ�T1�ٴζ�ȡ�����ݼ�¼���͵�һ�β�һ��

**�������**

- �����ϣ�Ϊ���Ⲣ�����µ����⣬����Ӧ��ȫ���뿪����˳��ִ�У������ܻ�ܲ
- ʵ���ϣ������Խϵ͵ĸ��뼶��������
- ����ĸ��뼶��Ҫ�õ��ײ����ݿ������֧��, ������Ӧ�ó�����߿�ܵ�֧��

### 4.2.1. Spring֧�ֵĸ��뼶��

1. Default,ʹ�õײ����ݿ�Ĭ�ϵļ��𣬴󲿷ֶ���read_commited����oracle
2. Read_uncommited,�����ȡδ�����������ύ�ı����3�����ⶼ�����
3. Read_commited��**ֻ�����ȡ���ύ�ı�����ɱ������**��������2�������Ի����
4. Repeatable_read����֤��ĳ�ֶζ�ζ�ȡ��ֵͬ�������ڼ��ֹ���������޸ģ����Ա�������������ظ��������ö��Դ���
5. Serializable����֤��ζ�ȡ��ͬ���У������ڼ��ֹ����������룬���º�ɾ�������Ա���3�����⣬�����ܺܲ�

**Oracle֧��Read_commited,Serializable��Mysql��֧��**

### 4.2.2. ����

1) ע�⣺@Transactional �� isolation����

```
@Transactional(isolation = Isolation.READ_COMMITTED)
```

2) xml��ʽ��

```
<tx:advice><tx:method Isolation=����>
```

## 4.3. ����ع�����

Ĭ�������ֻ��δ����쳣(RuntimeException��Error���͵��쳣)�ᵼ������ع�. ���ܼ���쳣����

### 4.3.1. ����

1) ע�ⷽʽ
@Transactional ע��� rollbackFor �� noRollbackFor ���������壬��ָ������쳣��
�C rollbackFor: ����ʱ������лع�
�C noRollbackFor: һ���쳣�࣬����ʱ���벻�ع�
2) XML��ʽ

```
<tx:advice><tx:method rollback-for=���� no-rollback-for=���� >
```

## 4.4. ��ʱ��ֻ������

### 4.4.1. ����

1) ע�ⷽʽ��@Transactional ע���readOnly timeout
2) XML��ʽ��

```
tx:advice<tx:method read-only=���� timeout=����>
```

