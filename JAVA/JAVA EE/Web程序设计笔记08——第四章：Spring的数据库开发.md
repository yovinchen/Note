# 第四章：Spring的数据库开发

JDBC: Java Database Connector
JdbcTemplate方法

## 一、execute()

#### (1)mysql创建数据库spring

```mysql
create database spring;
```

#### (2)导包

五个核心包：commons-logging spring-core spring-beans spring-context spring-expression
其他两个包：spring-jdbc mysql-connector-java

```java
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.3.6.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.28</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>4.3.6.RELEASE</version>
    </dependency>
```

#### (3)创建配置文件a.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
<!--1.配置数据源：    创建DriverManagerDataSource对象，连接数据库-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/spring"/>
        <property name="username" value="root"/>
        <property name="password" value="8520"/>
    </bean>

<!--    2.创建JdbcTemplate类对象-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
<!--    创建实现类对象-->
    <bean id="accountDao" class="com.gzh.dao.AccountDaoImpl">
        <property name="jdbcTemplate" ref="jdbcTemplate"/>
    </bean>
</beans>
```

#### (4)测试execute(）方法

```java
package com.gzh;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.jdbc.core.JdbcTemplate;

public class JdbcTest {
    public static void main(String[] args) {
        ApplicationContext applicationContext =new ClassPathXmlApplicationContext("a.xml");
        JdbcTemplate jdbcTemplate = (JdbcTemplate) applicationContext.getBean("jdbcTemplate");
        String sql="create table account(id int primary key auto_increment,username varchar (50),balance double)";
        jdbcTemplate.execute(sql);
    }
}
```

运行结果如下：数据库中已经成功创建表account

![image-20220329195454770](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220329195454770.png)

## 二、update()：增删改

实体类对象   转换  表中记录

###     实体类：

​    （1）和表对应，一张表对应一个实体类

​    （2）实体类中的成员变量    表中字段一一对应（数据类型   名称）

​    （3）实体类中的作用就是和表进行数据传输

SSM项目分三层：dao层    服务层     控制层，此代码为了简便，只创建dao层

​        dao层：
​        接口：接口中所有方法就是系统的功能。  面向接口编程
​        实现类：声明了一个JdbcTemplate类对象

###     步骤：

#### （1）创建实体类

```java
package com.gzh.domain;

public class Account {
    private int id;
    private String username;
    private double balance;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public double getBalance() {
        return balance;
    }

    public void setBalance(double balance) {
        this.balance = balance;
    }

    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", balance=" + balance +
                '}';
    }
}
```

#### （2）创建接口

```java
package com.gzh.dao;

import com.gzh.domain.Account;

public interface AccountDao {
    //增加记录
    int addAccount(Account account);
    
    //删除根据用户ID删除账户
    int deleteById(int id);

    //更新记录
    int updateAccount(Account account);
}
```

#### （3）实现接口

```java
package com.gzh.dao;

import com.gzh.domain.Account;
import org.springframework.jdbc.core.JdbcTemplate;

public class AccountDaoImpl implements AccountDao {
    private JdbcTemplate jdbcTemplate;

    public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    //增加记录
    @Override
    public int addAccount(Account account) {
        String sql = "insert into account values(?,?,?)";
        int update = jdbcTemplate.update(sql, account.getId(), account.getUsername(), account.getBalance());
        return update;
    }

    //删除根据用户ID删除账户
    @Override
    public int deleteById(int id) {
        String sql = "delete from account where id=?";
        int update = jdbcTemplate.update(sql, id);
        return update;
    }

    //更新记录
    @Override
    public int updateAccount(Account account) {
        String sql = "update account set username=?,balance=? where id=?";
        int update = jdbcTemplate.update(sql, account.getUsername(), account.getBalance(), account.getId());
        return update;
    }
}
```

#### （4）写配置文件

```java
<!--	1.配置数据源：    创建DriverManagerDataSource对象，连接数据库-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/spring"/>
        <property name="username" value="root"/>
        <property name="password" value="8520"/>
    </bean>

<!--    2.创建JdbcTemplate类对象-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"/>
    </bean>
        
<!--    3.创建实现类对象-->
    <bean id="accountDao" class="com.gzh.dao.AccountDaoImpl">
        <property name="jdbcTemplate" ref="jdbcTemplate"/>
    </bean>
```

#### （5）测试Test方法

```java
package com.gzh;

import com.gzh.dao.AccountDao;
import com.gzh.domain.Account;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class JdbcTest {
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("a.xml");
    AccountDao accountDao = (AccountDao) applicationContext.getBean("accountDao");

    @Test
    //增加记录
    public void addAccountTets() {
        Account account = new Account();
        account.setId(1);
        account.setUsername("zhangsan");
        account.setBalance(1000);
        accountDao.addAccount(account);
    }

    @Test
    //删除根据用户ID删除账户
    public void deleteByIdTest() {
        accountDao.deleteById(1);
    }

    @Test
    //更新记录
    public void updateAccountTest() {
        Account account = new Account();
        account.setUsername("zhangsan");
        account.setBalance(3000);
        account.setId(1);
        accountDao.updateAccount(account);
    }
}
```

#### 运行截图：

1.创建两个数据

![image-20220331221520933](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220331221520933.png)

2.测试删除数据

![image-20220331221604934](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220331221604934.png)

3.更新数据

![image-20220331221704292](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220331221704292.png)

###  补充：

#### 1.Object

当方法形参个数不确定的时候，可以使用Object...，代表的是多个多种类型的参数

#### 2.基本数据类型    包装类

​    int Integer
​    double Double

```
int a = 5;
Integer b = a;  //装箱
int c = b;  //拆箱
```

#### 3.单元测试

可以直接运行一个非main方法的方法	@Test

```java
public class AccountDao{
	@Test
    public void cat(){
        System.out.println("a");
    }
    
    @Test
    public void cat(){
        System.out.println("b");
    }
}
```

## 三、query(）：查

表中的记录 转换  实体类对象

### 步骤：

#### （1）创建实体类

同上

#### （2）创建接口

```java
//查询  根据id进行查询  单条记录
Account findAccountById(int id);

//查所有记录
List<Account> findAllAccounts();
```

#### （3）实现接口

```java
//查询  根据id进行查询  单条记录
@Override
public Account findAccountById(int id) {
    String sql = "select * from account where id =?";
    RowMapper<Account> rowMapper = new BeanPropertyRowMapper<>(Account.class);
    Account account = jdbcTemplate.queryForObject(sql, rowMapper, id);
    return account;
}

//查所有记录
@Override
public List<Account> findAllAccounts() {
    String sql = "select * from account";
    RowMapper<Account> rowMapper = new BeanPropertyRowMapper<Account>(Account.class);
    List<Account> account = jdbcTemplate.query(sql, rowMapper);
    return account;
}
```

#### （4）写配置文件

同上

#### （5）测试Test方法

```java
@Test
//查询  根据id进行查询  单条记录
public void findAccountByIdTest() {
    Account accountById = accountDao.findAccountById(1);
    System.out.println(accountById);
}

@Test
//查所有记录
public void findAllAccoundsTest() {
    List<Account> accounts = accountDao.findAllAccounts();
    for (Account i : accounts) {
        System.out.println(i);
    }
}
```

#### 运行截图：

1.单个查询

![image-20220331223326855](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220331223326855.png)

2.多项查询

![image-20220331223422159](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220331223422159.png)

