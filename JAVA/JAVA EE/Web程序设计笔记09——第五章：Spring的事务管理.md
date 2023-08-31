# 第五章：Spring的事务管理

transfer()的语句要么全做，要么全不做? 

```java
把transfer方法做成事务方法。

事务方法：方法体中的代码要么全部，要么全不做。
```

如何把transfer(）做成事务方法呢？

```java
1.基于xml文件	
2.基于注解
```

AOP关键导包

```java
<dependency>
      <groupId>org.aspectj</groupId >
      <artifactId>aspectjweaver</artifactId >
      <version>1.8.7</version >
</dependency>
```

以转钱为例，在未进行事务管理之前转账记录在遇到异常会将方法执行一半，这样会使部分资金转账失效，并且无退回，用事务管理之后方法不会因为异常而执行一半，只有**完全执行**和**不执行**。

## 一、基于XML方式的声明式事务

#### （1）创建实体类

同上

#### （2）创建接口

```java
//转钱outUser给inUser转money钱
void transfer(String outUser, String inUser, double money);
```

#### （3）实现接口

```java
//转钱outUser给inUser转money钱
@Override
public void transfer(String outUser, String inUser, double money) {
    String sql = "update account set balance=balance-? where username=?";
    jdbcTemplate.update(sql, money, outUser);
    //模拟异常
    System.out.println(1 / 0);
    String sql1 = "update account set balance=balance+? where username=?";
    jdbcTemplate.update(sql1, money, inUser);
}
```

#### （4）写配置文件

在上一章文件中增添以下配置

```java
<!--    基于事务管理器，以来于数据源-->
    <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
        
<!--    (1).基于XML方式的声明式事务-->
    <tx:advice id="txAdvice" transaction-manager="dataSourceTransactionManager">
        <tx:attributes>
            <tx:method name="*" propagation="REQUIRED" isolation="DEFAULT" read-only="false"/>
        </tx:attributes>
    </tx:advice>

    <aop:config>
        <aop:pointcut id="txPointCut" expression="execution(void com.gzh.dao.AccountDaoImpl.transfer(String,String,double))"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
    </aop:config>
```

#### （5）测试Test方法

```java
@Test
//转钱outUser给inUser转money钱
public void transferTest() {
    accountDao.transfer("zhangsan", "lisi", 500);
}
```

#### 运行截图：

1.无异常

![image-20220331225557619](https://lsky.hhdxw.top/imghub/img/image-20220331225557619.png)

2.有异常

![image-20220331225618374](https://lsky.hhdxw.top/imghub/img/image-20220331225618374.png)

## 二、基于Annotation（注解）方式的声明式事务

#### （1）创建实体类

同上

#### （2）创建接口

同上

#### （3）实现接口

```java
//在实现类上添加注解
@Transactional(propagation = Propagation.REQUIRED, isolation = Isolation.DEFAULT, readOnly = false)
```

#### （4）写配置文件

在上一章文件中增添以下配置

```java
<!--    基于事务管理器，以来于数据源-->
    <bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
        
<!--    (2).基于注解方式声明式事务-->
    <tx:annotation-driven transaction-manager="dataSourceTransactionManager"/>
```

#### （5）测试Test方法

同上

#### 运行截图：

1.无异常：

![image-20220331225721435](https://lsky.hhdxw.top/imghub/img/image-20220331225721435.png)

2.有异常：

![image-20220331225754769](https://lsky.hhdxw.top/imghub/img/image-20220331225754769.png)

