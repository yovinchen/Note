# 第十章：Spring 和 MyBatis 的整合

基于SSM的用户管理系统的设计与实现

## 基于传统的DAO方式的整合

#### 0.导包 

mybatis mybatis-spring spring mysql spring-tx spring-jdbc commons-dbcp2

```java
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>3.4.2</version>
</dependency>
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis-spring</artifactId>
  <version>1.3.1</version>
</dependency>
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>4.3.6.RELEASE</version>
</dependency>
<dependency>
  <groupId>org.apache.commons</groupId>
  <artifactId>commons-dbcp2</artifactId>
  <version>2.1.1</version>
</dependency>
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.28</version>
</dependency>
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
  <scope>compile</scope>
</dependency>
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-jdbc</artifactId>
  <version>4.3.6.RELEASE</version>
</dependency>
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-tx</artifactId>
  <version>4.3.6.RELEASE</version>
</dependency>
```

#### 1.准备数据 

mybatis 中的 t_customer

![image-20220426132557267](https://lsky.hhdxw.top/imghub/img/image-20220426132557267.png)

#### 2.创建实体类

```java
//在com.gzh.domain下创建
private int id;
private String username;
private String jobs;
private String phone;
//利用 getter ， setter 和 toString 方法生成代码
```

#### 3.在resources下创建CustomerMapper.xml文件

```java
<!--    1.根据id查找用户-->
    <select id="findCustomerById" parameterType="int" resultType="com.gzh.domain.Customer">
        select * from t_customer where id=#{id}
    </select>
</mapper>
```

#### 4.在resources下创建mybatis-config.xml文件

```java
<!--加载Mapper文件-->
      <mappers>
            <mapper resource="CustomerMapper.xml"/>
      </mappers>
```

#### 5.创建DAO层接口和实现类

```java
//在com.gzh.dao包下创建接口
public interface CustomerDao {
    Customer findCustomerById(int id);
}
```

```java
//在com.gzh.dao.impl包下创建实现类
public class CustomerDaoImpl extends SqlSessionDaoSupport implements CustomerDao {
    @Override
    public Customer findCustomerById(int id){
        Customer o = getSqlSession().selectOne("CustomerMapper.findCustomerById", id);
        return o;
    }
}
```

#### 6.在resources下创建applicationContext.xml文件

```java
<!--    1.连接数据库-->
    <bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
        <property name="username" value="root"/>
        <property name="password" value="8520"/>
    </bean>
<!--    2.加载MyBAtis的配置文件-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="configLocation" value="classpath:mybatis-config.xml"/>
    </bean>
<!--    3.创建接口对象-->
    <bean id="customerDao" class="com.gzh.dao.impl.CustomerDaoImpl">
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>
```

#### 7.测试

```java
//在com.gzh.domain包下创建测试类
@Test
public void findCustomerByIdTest(){
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
    CustomerDao customerDao = (CustomerDao) applicationContext.getBean("customerDao");
    Customer customerById = customerDao.findCustomerById(1);
    System.out.println(customerById);
}
```

![image-20220426134717655](https://lsky.hhdxw.top/imghub/img/image-20220426134717655.png)

## 基于Mapper方式的整合

### 基于MapperFactoryBean的整合

#### 0.导包

与DAO方式导包数量相同

#### 1.准备数据

同上数据

#### 2.创建实体类

```java
//在com.gzh.domain下创建
private int id;
private String username;
private String jobs;
private String phone;
//利用 getter ， setter 和 toString 方法生成代码
```

#### 3.创建com.gzh.mapper. CustomerMapper接口文件

```java
public interface CustomerMapper {
    Customer findCustomerById(int i);
}
```

#### 4.在resources下创建com->gzh->mapper->CustomerMapper.Xml文件

com.gzh.mapper 文件夹要一个一个创建，才能保证是嵌套的

```java
<mapper namespace="com.gzh.mapper.CustomerMapper">
<!--1.根据id查询客户信息-->
    <select id="findCustomerById" parameterType="int" resultType="com.gzh.domain.Customer">
        select * from t_customer where id=#{id}
    </select>
</mapper>
```

#### 5.在resources下创建mybatis-config.xml文件

```java
<mappers>
      <mapper resource="com/gzh/mapper/CustomerMapper.xml"/>
</mappers>
```

#### 6.在resources下创建applicationContext.xml文件

```java
<!--    1.连接数据库-->
<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
    <property name="username" value="root"/>
    <property name="password" value="8520"/>
</bean>
<!--    2.加载MyBAtis的配置文件-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <property name="configLocation" value="classpath:mybatis-config.xml"/>
</bean>
<!--    3.创建MapperFactoryBean类对象-->
<bean id="factoryBean" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="mapperInterface" value="com.gzh.mapper.CustomerMapper"/>
    <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
</bean>
```

#### 7.测试

```java
@Test
public void findCustomerById(){
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
    CustomerMapper factoryBean = (CustomerMapper) applicationContext.getBean("factoryBean");
    Customer customerById = factoryBean.findCustomerById(1);
    System.out.println(customerById);
}
```

![image-20220426135752830](https://lsky.hhdxw.top/imghub/img/image-20220426135752830.png)

虽然使用Mapper接口编程的方式很简单，但是在具体使用时还是需要遵循以下规范。

> (I)Mapper接口的名称和对应的Mapper.xml映射文件的名称必须一致。
>
> (2)Mapper,.xml文件中的namespace与Mapper接口的类路径相同（即接口文件和映射文件
> 需要放在同一个包中)。
>
> (3)Mapper接口中的方法名和Mapper.xml中定义的每个执行语句的id相同。
>
> (4)Mapper接口中方法的输入参数类型要和Mapper,.xml中定义的每个sql的parameterType
> 的类型相同。
>
> (5)Mapper接口方法的输出参数类型要和Mapper.xml中定义的每个sql的resultType的
> 类型相同。

只要遵循了这些开发规范，MyBatis就可以自动生成Mapper接口实现类的代理对象，从而
简化我们的开发。

### 基于MapperScannerConfigurer的整合

在上面的基础上修改

1.在 applicationContext 中注释以下代码

```java
<!--    3.创建MapperFactoryBean类对象-->
    <bean id="factoryBean" class="org.mybatis.spring.mapper.MapperFactoryBean">
        <property name="mapperInterface" value="com.gzh.mapper.CustomerMapper"/>
        <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    </bean>
```

2.在 applicationContext 中添加以下代码

```java
<!--    4.基于MapperScannerConfigurer的整合-->
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="basePackage" value="com.gzh.mapper"/>
</bean>
```

3.创建新的测试类

```java
@Test
public void findCustomerById1(){
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
    CustomerMapper bean = applicationContext.getBean(CustomerMapper.class);
    Customer customerById = bean.findCustomerById(1);
    System.out.println(customerById);
}
```

![image-20220427102429052](https://lsky.hhdxw.top/imghub/img/image-20220427102429052.png)