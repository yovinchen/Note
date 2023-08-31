# 第九章：MyBatis 的关系映射

> 实体和实体之间： 1:1  1:多  多:多

# 基于 MyBatis 的客户管理系统的设计与设计

## 功能：

### 一、根据id查询客户的所有信息(id,name,age,sex,Car_id,code)

> 进行关联查询的方式有两种：
>
> 1.嵌套查询：需要写多个Mapper文件，也就需要写多条SQL语句，但是每一条SQL语句都很简单。
>
> 2.嵌套结果：只需要写一个Mapper文件，也就是只需要写一条SQL语句，但是这一条sQL语句比较复杂

#### 1.准备数据

创建数据库 tb_idcard 和 tb_person两个表

```mysql
create table tb_idcard(
id int primary key auto_increment,
code varchar(18)
);
```

```mysql
insert into tb_idcard(code) values
('154326874565214862'),
('13254868459587852');
```

```mysql
create table tb_person(
id int primary key auto_increment,
name varchar(32),
age int,
sex varchar(8),
card_id int unique,
foreign key(card_id) references tb_idcard(id)
);
```

```mysql
insert into tb_person(name,age,sex,card_id) values
('Rose',20,'女',1),
('Tom',20,'男',2);
```

![image-20220419102233809](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220419102233809.png)

#### 2.创建项目，导入依赖：mybatis mysql

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

#### 3.创建实体类

```java
//tb_idcard 
private int id;
private String Code;
利用 getter ， setter 和 toString 方法生成代码
```

```java
//tb_person
private int id;
private String name;
private int age;
private String sex;
//注意，此成员变量是另一个实体类的对象，反映的是实体脚erson.和实体类红dcard,的：1的关系
private IdCard card;
利用 getter ， setter 和 toString 方法生成代码
```

#### 4.创建 IdCardMapper.xml PersonMapper.xml 文件

```java
<mapper namespace="IdCardMapper">
<!--根据 id 查询证件信息-->
    <select id="findCodeById" parameterType="int" resultType="com.gzh.domain.IdCard">
        select  * from tb_idcard where id=#{id}
    </select>
</mapper>
```

```java
<mapper namespace="PersonMapper">
<!--    1.根据 id 查询用户信息-->
    <resultMap id="a" type="com.gzh.domain.Person">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="age" column="age"/>
        <result property="sex" column="sex"/>
        <association property="card" column="card_id" javaType="com.gzh.domain.IdCard"
                select="IdCardMapper.findCodeById"/>
    </resultMap>
    <select id="findPersonById" resultMap="a" >
        select * from tb_person where id=#{id}
    </select>
</mapper>
```

#### 5.创建 mybatis-config.xml 文件

```java
<configuration>
<!--1.连接数据库-->
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="8520"/>
            </dataSource>
        </environment>
    </environments>
<!--2.加载数据库-->
    <mappers>
        <mapper resource="IdCardMapper.xml"/>
        <mapper resource="PersonMapper.xml"/>
    </mappers>
</configuration>
```

#### 6.复制工具类 MyBatisUtils

```java
//对外提供 sqlSession 对象
public class MyBatisUtils {
    private static SqlSessionFactory sqlSessionFactory = null;

    static {
        try {
            InputStream inputStream = Resources.getResourceAsStream("mybatis-config.xml");
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static SqlSession getSession() {
        return sqlSessionFactory.openSession();
    }
}
```

#### 7.测试类

```java
public class MyBatisTest {
    SqlSession sqlSession = MyBatisUtils.getSession();
    @Test
    public void findPersonByIdTest(){
        Person o = sqlSession.selectOne("PersonMapper.findPersonById",1);
        System.out.println(o);
        sqlSession.close();
    }
}
```

运行截图：

![image-20220419112333746](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220419112333746.png)

### 二、使用嵌套结果：根据id查询客户的所有信息(id,name,age,sex,Car_id,code)

#### 1.mybatis-config.xml 中修改：注释掉以下语句

```java
<!--        <mapper resource="IdCardMapper.xml"/>-->
```

#### 2.PersonMapper

```java
<!--    根据嵌套结果来做查询-->
    <resultMap id="b" type="com.gzh.domain.Person">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="age" column="age"/>
        <result property="sex" column="sex"/>
        <association property="card" javaType="com.gzh.domain.IdCard">
            <id property="id" column="card_id"/>
            <result property="code" column="code"/>
        </association>
    </resultMap>
    <select id="findPersonById2" parameterType="int" resultMap="b">
        select p.*,idcard.code
        from tb_person p,tb_idcard idcard
        where p.card_id=idcard.id and p.id=#{id}
    </select>
```

#### 3.测试

```java
public class MyBatisTest {
    SqlSession sqlSession = MyBatisUtils.getSession();
    @Test
    public void findPersonByIdTest(){
        Person o = sqlSession.selectOne("PersonMapper.findPersonById2",1);
        System.out.println(o);
        sqlSession.close();
    }
}
```

![image-20220419120201332](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220419120201332.png)







