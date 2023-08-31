# 第七章：MyBatis核心配置

## 7.1 核心对象
### 1.复制第6章的代码

#### （1）导包 mybatis  mysql

```java
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
</dependency>
```

#### （2）准备数据

沿用上一章节数据

#### （3）复制实体类

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

#### （4）复制两个xml文件

mybatis-config.xml：

```java
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd" >
<configuration>
    <!--1.连接数据库-->
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
    		<!--
            transactionManager：设置事务管理方式
            属性：
	            type：设置事务管理方式，type="JDBC|MANAGED"
	            type="JDBC"：设置当前环境的事务管理都必须手动处理
	            type="MANAGED"：设置事务被管理，例如spring中的AOP
            -->
            <dataSource type="POOLED">
    		<!--
            dataSource：设置数据源
            属性：
	            type：设置数据源的类型，type="POOLED|UNPOOLED|JNDI"
	            type="POOLED"：使用数据库连接池，即会将创建的连接进行缓存，下次使用可以从缓存中直接获取，不需要重新创建
	            type="UNPOOLED"：不使用数据库连接池，即每次使用连接都需要重新创建
	            type="JNDI"：调用上下文中的数据源
            -->
                <!--四个成员变量不能错-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="8520"/>
            </dataSource>
        </environment>
    </environments>
    <!--2.加载Mapper文件-->
    <mappers>
    <!--
        以包为单位，将包下所有的映射文件引入核心配置文件
        注意：
			1. 此方式必须保证mapper接口和mapper映射文件必须在相同的包下
			2. mapper接口要和mapper映射文件的名字一致
        -->
        <!--resource 从 copy path（复制路径/引用）中 file name(文件名)-->
        <mapper resource="CustomerMapper.xml"/>
    </mappers>
</configuration>
```

CustomerMapper.xml：

```java
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<!--    namespace属性的值：copy path 中 file name(复制引用中的文件名),去掉.xml-->
<mapper namespace="CustomerMapper">
<!--    1.根据id查询客户信息-->
    <select id="findCustomerById" parameterType="int" resultType="com.gzh.po.Customer">
        select * from t_customer where id=#{id}
    </select>
<!--    2.根据客户名进项模糊搜索-->
    <select id="findCustomerByName" parameterType="String" resultType="com.gzh.po.Customer">
        select * from t_customer where username like '%${value}%'
    </select>
<!--    3.添加客户-->
    <insert id="addCustomer" parameterType="com.gzh.po.Customer">
        insert  into t_customer values (#{id},#{username},#{jobs},#{phone})
    </insert>
<!--    4.更新客户-->
    <update id="updateCustomer" parameterType="com.gzh.po.Customer">
        update t_customer set username = #{username},jobs = #{jobs},phone = #{phone} where id = #{id}
    </update>
<!--    5.删除客户-->
    <delete id="deleteCustomer" parameterType="int">
        delete from t_customer where id = #{id}
    </delete>
</mapper>
```

#### （5）测试类

```java
package com.gzh.po;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

public class MybatisTest {
    InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(resourceAsStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();

    public MybatisTest() throws IOException {
    }

    @Test
    public void findCustomerById() {
        Customer o = sqlSession.selectOne("CustomerMapper.findCustomerById", 2);
        System.out.println(o);
    }
```

运行结果如下：

![image-20220412211705869](https://lsky.hhdxw.top/imghub/img/image-20220412211705869.png)

### 2.创建工具类，对外提供SqlSession对象

在 main 下创建一个新的包  com.gzh.utils （通常为工具包），并创建类 MyBatisUtil

```java
package com.gzh;

import com.gzh.po.Customer;
import com.gzh.utils.MyBatisUtil;
import org.apache.ibatis.session.SqlSession;
import org.junit.Test;

public class MyBatisTest {
    SqlSession sqlSession=MyBatisUtil.getSession();

    @Test
    public void findCustomerByIdTest(){
        Customer o = sqlSession.selectOne("CustomerMapper.findCustomerById", 2);
        System.out.println(o);
    }
}
```

将 Test 测试类转移到 com.gzh 包下，代码如下：

```java
public class MyBatisTest {
    SqlSession sqlSession=MyBatisUtil.getSession();
    @Test
    public void findCustomerByIdTest(){
        Customer o = sqlSession.selectOne("CustomerMapper.findCustomerById", 2);
        System.out.println(o);
    }
}
```

运行截图：

![image-20220412213246062](https://lsky.hhdxw.top/imghub/img/image-20220412213246062.png)



## 7.2 配置文件

### 1.properties 元素

连接数据库，通过该元素进行内部配置外在化。

(1) 在resources下创建db.properties

```java
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis
jdbc.username=root
jdbc.password=8520
```

(2) 修改配置文件  2处

1.在<configuration>文件下添加<properties>

```java
<properties resource="db.properties"/>
```

2.在<dataSource>标签中<property>的 value 属性修改如下：

```java
<property name="driver" value="${jdbc.driver}"/>
<property name="url" value="${jdbc.url}"/>
<property name="username" value="${jdbc.username}"/>
<property name="password" value="${jdbc.password}"/>
```

（3）测试运行截图

![image-20220413084844679](https://lsky.hhdxw.top/imghub/img/image-20220413084844679.png)

### 2.typeAliases元素 给实体类起别名

（1）修改CustomerMapper.xml中的：（修改resultType属性）

```java
<select id="findCustomerById" parameterType="int" resultType="customer">
    select * from t_customer where id=#{id}
</select>
```

（2）在mybatis-config.xml文件中起别名（添加）

```java
<properties resource="db.properties"/>
  <typeAliases>
     <typeAlias type="com.wqx.po.Customer" alias="customer"/>
</typeAliases>
```

（3）测试截图如下：

![image-20220413090235983](https://lsky.hhdxw.top/imghub/img/image-20220413090235983.png)

## 7.3 映射文件

### 1.resultMap元素:将表中字段和实体类成员变量进行对应

（1）修改实体类 username->name

重写getter and setter  以及   toString()

（2）测试结果：当实体类中的成员变量名和表中字段名字不一致时，导致字段值不能被赋值给成员变量

Customer{id=2, name='null', jobs='teacher', phone='13521210112'}

![image-20220413091252095](https://lsky.hhdxw.top/imghub/img/image-20220413091252095.png)

（3）如何解决当实体类中的成员变量名和表中字段名字不一致时，还可以将字段值赋值给成员变量？

resultMap

（4）修改CustomerMapper.xml

添加 resultMap 和 修改 select 中主键的 resultType 属性改为 resultMap

```java
<resultMap id="a" type="customer">
   <id property="id" column="id"/>
   <result property="name" column="username"/>
   <result property="jobs" column="jobs"/>
   <result property="phone" column="phone"/>
</resultMap>
<select id="findCustomerById" parameterType="int" resultMap="a">
   select * from t_customer where id=#{id}
</select>
```




---如何将sql语句显示到控制台？

（1）log4j.properties

```java
#Global logging configuration
log4j.rootLogger=ERROR,stdout
#MyBatis logging configuration...
log4j.logger.com=DEBUG
#Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t]-%m%n
```

（2）导包

```java
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>1.2.17</version>
</dependency>
```

（3）resources 下  创建com,把CustomerMapper.xml文件移动到com下

![image-20220413093052137](https://lsky.hhdxw.top/imghub/img/image-20220413093052137.png)

（4）修改两个xml文件

CustomerMapper.xml

```java
namespace="com.CustomerMapper"
```

mybatis-config.xml

```java
resource="com/CustomerMapper.xml"
```

（5）修改测试方法

```java
Customer o = sqlSession.selectOne("com.CustomerMapper.findCustomerById", 2);
```

（6）运行截图

![image-20220413092737690](https://lsky.hhdxw.top/imghub/img/image-20220413092737690.png)