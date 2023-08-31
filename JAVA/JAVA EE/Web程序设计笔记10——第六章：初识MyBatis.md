# 第6章：MyBatis

Spring: 负责实现业务层的逻辑

MyBatis：主要和数据库打交道

Spring MVC：主要负责前端页面

## 6.1 MyBatis

1.持久层框架： 负责数据库开发

2.ORM框架：Object/Relational Mapping

3.POJO(Plain Old Java Object)：普通的Java对象
   PO(Persisent Object):持久化对象

## 6.2

## 6.3

## 6.4 MyBatis的入门程序

基于MyBatis的客户管理系统的设计与实现

### 系统功能：

```java
1. 根据id查询客户
2. 根据姓名模糊查询
3. 添加客户
4. 更新客户
5. 删除客户
```

### 程序准备：

#### （1）mysql中创建数据库mybatis,表t_customer,插入数据。

```mysql
create database mybatis;
```

```mysql
create table t_customer(
id int (32) primary key auto_increment,
username varchar(50),
jobs varchar(50),
phone varchar(16)
);
```

```mysql
insert into t_customer values
('1','joy','doctor','13745874578'),
('2','jack','teacher','13521210112'),
('3','tom','worker','15179405961');
```

![image-20220405130626049](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405130626049.png)

#### （2）创建项目，导2个包：mybatis   mysql-connector-java

```java
  <dependencies>
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.4.2</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>8.0.28</version>
<!--      版本号与自己mysql版本一致-->
    </dependency>
```

#### （3）创建持久化类（实体类）

作用：和表进行数据转换
-- 增：实体类对象   转换  表中的记录
-- 查：表中的记录   转换  实体类对象
注意：成员变量的名字、数据类型   需要和  表中字段的名字、数据类型  保持一致

生成setter、getter 和 toString()方法

```java
package com.gzh.po;

public class Customer {
    private int id;
    private String username;
    private String jobs;
    private String phone;

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

    public String getJobs() {
        return jobs;
    }

    public void setJobs(String jobs) {
        this.jobs = jobs;
    }

    public String getPhone() {
        return phone;
    }

    public void setPhone(String phone) {
        this.phone = phone;
    }

    @Override
    public String toString() {
        return "Customer{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", jobs='" + jobs + '\'' +
                ", phone='" + phone + '\'' +
                '}';
    }
}
```

#### （4）在resources文件夹下创建CustomerMapper.xml文件，用来写sql语句

##### 1.首先创建模板

在 file （文件）中的 setting （设置）中搜索 File and Code Templates （文件和代码模板），在其中首页 Files （文件）下创建文件 Mapper.xml，然后在下面填入以下代码，选择 OK （确定） 退出

```java
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="">

</mapper>
```

![image-20220405131535640](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405131535640.png)

##### 2.创建CustomerMapper.xml文件

在 resources 文件夹右击选择 new（新建）Mapper

![image-20220405132203804](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405132203804.png)

##### 3.写sql语句

```java
1.namespace属性的值：copy path（复制路径/引用）中 file name(文件名),去掉.xml
    
<mapper namespace="CustomerMapper">

2.#{id}占位符
3.parameterType是来设置占位符对应的参数的数据类型
4.resultType用来设置接收查询结果的持久化类，注意的是resultType属性值是全类名（全限定名）
    
<select id="findCustomerById" parameterType="int"resultType="com.gzh.po.Customer">
   select * from t_customer where id=#{id}
</select>
```

![image-20220405132451651](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405132451651.png)

#### （5）在resources文件夹下创建mybatis-config.xml文件，该文件是MyBatis框架的配置文件

##### 1.首先创建模板

在 file （文件）中的 setting （设置）中搜索 File and Code Templates （文件和代码模板），在其中首页 Files （文件）下创建文件 mybatis-config.xml，然后在下面填入以下代码，选择 OK （确定） 退出

```java
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd" >
<configuration>
 
</configuration>
```

![image-20220405131954150](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405131954150.png)

##### 2.创建mybatis-config.xml文件

在 resources 文件夹右击选择 new（新建）mybatis-config 

![image-20220405132528897](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405132528897.png)

##### 3.配置MyBatis框架

```java
<configuration>
<!--      1.连接数据库-->
      <environments default="mysql">
            <environment id="mysql">
                  <transactionManager type="JDBC"></transactionManager>
                  <dataSource type="POOLED">
<!--                        四个成员变量不能错-->
                        <property name="driver" value="com.mysql.jdbc.Driver"/>
                        <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                        <property name="username" value="root"/>
                        <property name="password" value="8520"/>
                  </dataSource>
            </environment>
      </environments>
<!--      2.加载Mapper文件-->
      <mappers>
<!--            resource 从 copy path（复制路径/引用）中 file name(文件名)-->
            <mapper resource="CustomerMapper.xml"/>
      </mappers>

</configuration>
```

![image-20220405132915669](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405132915669.png)

#### （6）单元测试：查询

在 com.gzh.po 包下创建测试类MybatisTest

1.加载配置文件和 Mapper 文件

通过 CustomerMapper.xml 文件中的 select 的 id 的值来命名测试类

![image-20220405133745512](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405133745512.png)

在输入 **Resources** 时通过提示导入名为 org 开头的包

![image-20220405134057509](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405134057509.png)

在此输入完此行代码后 getResourceAsStream 会爆红，不用管后续处理

```java
InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
```

##### 2.构建会话工厂

```java
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
```

##### 3.构建会话 SqlSession

```java
SqlSession sqlSession = sqlSessionFactory.openSession();
```

因为要测试多个方法，所以将以上构造剪切到**成员变量**

![image-20220405134848712](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405134848712.png)

##### 4.处理爆红错误

将鼠标放置到爆红的 resourceAsStream 处 Alt+回车 处理问题，选择添加类默认构造函数签名的异常

![image-20220405134938312](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405134938312.png)

##### 5.导包 Test ，并写完查询类

selectOne（）中的参数 = CustomerMapper.xml文件中 namespace + select id

MybatisTest.java 总体代码如下：

```java
package com.gzh.po;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

public class MybatisTest {
    InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();

    public MybatisTest() throws IOException {
    }

    @Test
    public void findCustomerById(){
//        s: = CustomerMapper.xml文件中 namespace + select id
        Customer o = sqlSession.selectOne("CustomerMapper.findCustomerById", 1);
        System.out.println(o);
    }
}
```

运行结果如下：

![image-20220405135447683](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220405135447683.png)

### 1.根据id查询客户

```java
<select id="findCustomerById" parameterType="int" resultType="com.gzh.po.Customer">
    select * from t_customer where id=#{id}
</select>
```

```java
@Test
public void findCustomerById() {
   Customer o = sqlSession.selectOne("com.CustomerMapper.findCustomerById", 1);
   System.out.println(o);
}
```

![image-20220407152029547](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220407152029547.png)

### 2.根据姓名模糊查询

```java
<select id="findCustomerByName" parameterType="String" resultType="com.gzh.po.Customer">
    select * from t_customer where username like '%${value}%'
</select>
```

```java
@Test
public void findCustomerByName() {
    List<Customer> o = sqlSession.selectList("CustomerMapper.findCustomerByName", "j");
    for (Customer customer : o){
        System.out.println(customer);
    }
}
```

![image-20220407152044583](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220407152044583.png)

### 3.添加客户

```java
<insert id="addCustomer" parameterType="com.gzh.po.Customer">
    insert  into t_customer values (#{id},#{username},#{jobs},#{phone})
</insert>
```

```java
@Test
public void addCustomer(){
    Customer customer = new Customer();
    customer.setId(1);
    customer.setUsername("zhangsan");
    customer.setJobs("student");
    customer.setPhone("110");
    sqlSession.insert("CustomerMapper.addCustomer",customer);
    sqlSession.commit();//只要更改数据库中的数据，就需要调用该方法完成数据的
    sqlSession.close();//关闭数据库的连接
}
```

![image-20220407152231144](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220407152231144.png)

### 4.更新客户

```java
<update id="updateCustomer" parameterType="com.gzh.po.Customer">
    update t_customer set username = #{username},jobs = #{jobs},phone = #{phone} where id = #{id}
</update>
```

```java
@Test
public void updateCustomer() {
    Customer customer = new Customer();
    customer.setId(4);
    customer.setUsername("zhangsan");
    customer.setJobs("teacher");
    customer.setPhone("18888888888");
    sqlSession.update("CustomerMapper.updateCustomer", customer);
    sqlSession.commit();
    sqlSession.close();
}
```

![image-20220407152334704](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220407152334704.png)

### 5.删除客户

```java
<delete id="deleteCustomer" parameterType="int">
    delete from t_customer where id = #{id}
</delete>
```

```java
@Test
public void deleteCustomer() {
    sqlSession.delete("CustomerMapper.deleteCustomer", 1);
    sqlSession.commit();
    sqlSession.close();
}
```

![image-20220407152346163](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220407152346163.png)



### 如何在控制台输入SQL语句？

(1) 导包 log4j

```java
<dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
</dependency>
```

(2) 复制log4j.properties文件到resources.文件夹下

(3) resources下新建directory,名为com

(4) 将CustomerMapper.xml文件复制到com下

![image-20220407152650898](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220407152650898.png)

(5) 复制后的CustomerMapper.Xml中的namespacel的值修改：com.CustomerMapper

![image-20220407152719404](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220407152719404.png)

(6) 修改mybatis-config.xml文件中resource属性的值：

![image-20220407152735127](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220407152735127.png)

(7) 修改测试方法中的值：

![image-20220407152802930](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220407152802930.png)

运行测试结果如下：

![image-20220407152822914](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220407152822914.png)