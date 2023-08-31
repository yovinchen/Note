# 第八章：动态SQL

基于MyBatis的客户管理系统的设计与开发

## 1.复制第6章的代码

导包：mybatis mysql
准备数据
数据库：mybatis 表 t_customer
创建实体类Customer
创建CustomerMapper.xml文件，写SQL语句的
创建mybatis-config.xml文件，配置文件
创建工具类，对外提供
测试
在上一章节中有所展示记录

## 2.系统功能

### 1. if 元素

如果姓名和职业都不为空，按姓名和职业查找客户
如果姓名不为空职业为空，按姓名查找客户
如果姓名为空职业不为空，按职业查找客户
如果姓名和职业都为空，则查找所有客户。

#### 下面是 CustomerMapper.xml 文件中的 sql 语句：

```java
<!--
  1.如果姓名和职业都不为空，按姓名和职业查找客户
  select * from t_customer where 1=1 and username like concat('%',#{username},'%') and jobs=#{jobs}
  2.如果姓名不为空职业为空，按姓名查找客户
  select * from t_customer where 1=1 and username like concat('%',#{username},'%')
  3.如果姓名为空职业不为空，按职业查找客户
  select * from t_customer where 1=1 and jobs=#{jobs}
  4.如果姓名和职业都为空，则查找所有客户。
  select * from t_customer where 1=1
 -->
      
test="username!=null and username!=''"
此处的username是实体类Customer的成员变量,如果test中条件成立，and这一行代码就会被拼接到1=1的后面，否则，就不拼接

and username like concat('%',#{username},'%')
此处的第一个username是字段名，第2个username.是实体类Customer的成员变量  concat函数 连接字符串
```

```java
<select id="findCustomerByNameAndJobs" parameterType="com.gzh.po.Customer" resultType="com.gzh.po.Customer">
   select * from t_customer where 1=1
   <if test="username!=null and username!=''">
       and username like concat('%',#{username},'%')
   </if>
   <if test="jobs!=null and jobs!=''">
       and jobs=#{jobs}
   </if>
</select>
```

#### 测试类，通过名字进行查询：

```java
@Test
public void findCustomerByNameAndJobsTest(){
    Customer customer = new Customer();
    customer.setUsername("w");
    List<Customer> objects = sqlSession.selectList("CustomerMapper.findCustomerByNameAndJobs", customer);
    for (Customer object: objects) {
        System.out.println(object);
    }
}
```

#### 运行截图：

传一个名字，按名字查询：

![image-20220415134517801](https://lsky.hhdxw.top/imghub/img/image-20220415134517801.png)

不传参查询全部：

![image-20220415134721203](https://lsky.hhdxw.top/imghub/img/image-20220415134721203.png)

### 2. choose - when - otherwise 元素

当客户名不为空，只根据客户名查询
当客户名为空，职业不为空，则只根据职业查询
当客户名和职业都为空，则查询电话不为空的客户

#### 下面是 CustomerMapper.xml 文件中的 sql 语句：

```java
<!--
    2.当客户名不为空，只根据客户名查询
    select * from t_customer where 1=1 and username like concat('%',#{username},'%')
    当客户名为空，职业不为空，则只根据职业查询
    select * from t_customer where 1=1 and jobs=#{jobs}
    当客户名和职业都为空，则查询电话不为空的客户
    select * from t_customer where 1=1 and phone is not null
-->
```

```java
<select id="findCustomerByNameOrJobs" parameterType="com.gzh.po.Customer" resultType="com.gzh.po.Customer">
    select * from t_customer where 1=1
    <choose>
        <when test="username!=null and username!=''">
            and username like concat('%',#{username},'%')
        </when>
        <when test="jobs!=null and jobs!=''">
            and jobs=#{jobs}
        </when>
        <otherwise>
            and phone is not null
        </otherwise>
    </choose>
</select>
```

#### 测试类，通过职业进行查询：

```java
@Test
public void findCustomerByNameOrJobs(){
    Customer customer = new Customer();
    customer.setJobs("teacher");
    List<Customer> objects = sqlSession.selectList("CustomerMapper.findCustomerByNameOrJobs", customer);
    for (Customer object: objects) {
        System.out.println(object);
    }
}
```

#### 运行截图：

传职业，按职业查询：

![image-20220415134806665](https://lsky.hhdxw.top/imghub/img/image-20220415134806665.png)

不传参，查询有手机号：

![image-20220415134848949](https://lsky.hhdxw.top/imghub/img/image-20220415134848949.png)

### 3. where 元素

> 根据需要自动添加 where ，而且会自动保留或去掉 and 关键字

如果姓名和职业都不为空，按姓名和职业查找客户
如果姓名不为空职业为空，按姓名查找客户
如果姓名为空职业不为空，按职业查找客户
如果姓名和职业都为空，则查找所有客户。

#### 下面是 CustomerMapper.xml 文件中的 sql 语句：

```java
<select id="findCustomerByNameAndJobs1" parameterType="com.gzh.po.Customer" resultType="com.gzh.po.Customer">
   select * from t_customer
   <where>
       <if test="username!=null and username!=''">
           and username like concat('%',#{username},'%')
       </if>
       <if test="jobs!=null and jobs!=''">
           and jobs=#{jobs}
       </if>
   </where>
</select>
```

#### 测试类，通过姓名进行查询：

```java
@Test
public void findCustomerByNameAndJobs1Test(){
    Customer customer = new Customer();
    customer.setUsername("j");
    List<Customer> objects = sqlSession.selectList("CustomerMapper.findCustomerByNameAndJobs1", customer);
    for (Customer object: objects) {
        System.out.println(object);
    }
}
```

#### 运行截图：

传一个名字，按名字查询：

![image-20220415135501209](https://lsky.hhdxw.top/imghub/img/image-20220415135501209.png)

不传参查询全部：

![image-20220415135537915](https://lsky.hhdxw.top/imghub/img/image-20220415135537915.png)

### 4. set 元素

> 根据id更新客户信息 set元素根据需要自动去掉多余的逗号

通过 ID 更新信息

#### 下面是 CustomerMapper.xml 文件中的 sql 语句：

```java
<select id="updateCustomer" parameterType="com.gzh.po.Customer"> update t_customer
    <set>
        <if test="username!=null and username!=''">
            username=#{username},
        </if>
        <if test="jobs!=null and jobs!=''">
            jobs=#{jobs},
        </if>
        <if test="phone!=null and phone!=''">
            phone=#{phone},
        </if>
    </set>
        where id=#{id}
</select>
```

测试类，通过 ID 进行修改：

```java
@Test
public void updateCustomer(){
    Customer customer = new Customer();
    customer.setId(3);
    customer.setUsername("maliu");
    sqlSession.selectList("CustomerMapper.updateCustomer", customer);
    sqlSession.commit();
    sqlSession.close();
}
```

运行截图：

![image-20220415135735652](https://lsky.hhdxw.top/imghub/img/image-20220415135735652.png)