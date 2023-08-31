#  第二章：Spring中的Bean

## 9.基于XML的装配

### 什么是Bean的装配？

Bean的装配可以理解为依赖关系注入，Bean的装配方式即Bean依赖注入的方式。Spring容器支持多种形式的Bean的装配方式，如基于XML的装配、基于注解（Annotation）的装配和自动装配（其中最常用的是基于注解的装配），本节将主要讲解这三种装配方式的使用。



1.在 java 包下创建名为 com.ssm.assemble 的包并在包下创建名为 User 的类，输入（利用生成构造函数、setter方法和toString方法）

代码如下：

```java
private String username;
    private Integer password;
    private List<String> list;

//    1.使用构造注入
//    1.1提供所有参数的有参构造方法。

    public User(String username, Integer password, List<String> list) {
        this.username = username;
        this.password = password;
        this.list = list;
    }

//    2.使用设值注入
//    2.2提供默认空参构造方法
//    2.3为所有属性提供setter方法


    public User() {
        super();
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public void setPassword(Integer password) {
        this.password = password;
    }

    public void setList(List<String> list) {
        this.list = list;
    }

    @Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                ", password=" + password +
                ", list=" + list +
                '}';
    }
```

2.在 resources 文件下创建名为 beans5.xml 的文件并通过两种方法装配User实例

代码如下：

```java

    <!--    1——1.使用构造方法注入方式装配User实例-->
    <bean id="user1" class="com.ssm.assemble.User">
        <constructor-arg index="0" value="tom"/>
        <constructor-arg index="1" value="123456"/>
        <constructor-arg index="2">
            <list>
                <value>"constructorvalue1</value>
                <value>"constructorvalue2</value>
            </list>
        </constructor-arg>
    </bean>

    <!--    1——2.使用设值注入方式装配User实例-->
    <bean id="user2" class="com.ssm.assemble.User">
        <property name="username" value="张三"></property>
        <property name="password" value="654321"></property>
    <!--        注入list集合-->
        <property name="list">
            <list>
                <value>"setlistvalue1"</value>
                <value>"setlistvalue2"</value>
            </list>
        </property>
    </bean>
```

3.在  com.ssm.assemble 包下创建 XmlBeanAssembleTest 测试类，并运行程序

代码如下：

```java
//        定义配置文件路径
        String xmlPath = "beans5.xml";
//        加载配置文件
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);
//        构造方法输出结果
        System.out.println(applicationContext.getBean("user1"));
//        构造方法输出结果
        System.out.println(applicationContext.getBean("user2"));
```

运行结果如图：

![image-20220310180658138](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220310180658138.png)

### 总结：基于XML的装配使用方式：

:one:创建 Java 类，提供有参无参构造方法以及属性 setter 方法

:two:创建 Spring 的 xml 配置文件使用两种方式配置Bean；

:three:创建测试类，测试程序

## 10.基于Annotation的装配

通过在类上方和成员变量上方添加注解

SSM项目分三层：
Dao层：接口和实现类——和数据库打交道

```java
//UserDaoImpl	类注解
@Repository("userDao")
```

服务层：接口和实现类——调用Dao层

```java
//UserServiceImpl	类注解
@Service("userService")

//UserServiceImpl	成员变量注解
@Resource(name = "userDao")
```

控制层：类——调用服务层

```java
//UserController	类注解
@Resource(name = "userDao")
//UserController	成员变量注解
@Controller("userController")
```



### 1.首先实现三层结构：

创建 com.ssm.annotation 包并在其包下进行以下操作

##### Dao层

创建 UserDao 接口，代码如下：

```java
package com.ssm.annotation;

public interface UserDao {
    public void save();
}
```

创建 UserDaoImpl 实现类，代码如下：

```java
package com.ssm.annotation;

public class UserDaoImpl implements UserDao {
    @Override
    public void save() {
        System.out.println("userDao...save...");
    }
}
```

##### 服务层

创建 UserService 接口，代码如下：

```java
package com.ssm.annotation;

public interface UserService {
    public void save();
}
```

创建 UserServiceImpl 实现类，代码如下：

```java
package com.ssm.annotation;

public class UserServiceImpl implements UserService{
    private UserDao userDao;
    @Override
    public void save(){
        userDao.save();
        System.out.println("Userservice...save...");
    }
}
```

可以称为服务层调Dao层

##### 控制层

创建一个 UserController 控制器，代码如下：

```java
package com.ssm.annotation;

public class UserController {
    private UserService userService;
    public void save() {
        userService.save();
        System.out.println("userController...save...");
    }
}
```

### 2.装配

对Dao层的实现类上方添加注解

```java
//相当于在xml文件中写<bean id="userDao" class="com.ssm.annotaion.UserDaoImpl"/>
@Repository("userDao")
//Repository 仓库 Dao层需要数据库打交道
```

对服务层的实现类以及成员变量上方添加注解

```java
//相当于在xml文件中写<bean id="UserService" class="com.ssm.annotaion.UserServiceImpl"/>
@Service("userService")

@Resource(name = "userDao")
```

对服务层的实现类和成员变量上方添加注解

```java
@Controller("userController")

@Resource(name = "userService")
```

创建 beans6.xml 文件，利用xml文件扫描这些包来实现装配

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.ssm.annotation"/>
</beans>
```

创建 Test 测试类

```java
package com.ssm.annotation;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("beans6.xml");
        UserController userController = (UserController) applicationContext.getBean("userController");
        userController.save();
    }
}
```

装配成功运行结果如截图如下：

![image-20220311125821133](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220311125821133.png)

### 3.自动装配

将一个 Bean 自动的注入到其他 Bean 的 property 中，Spring 的<Bean>元素中包含一个 autwire 属性，我们可以通过设置 autowire 的属性值来自动装配 Bean 。

|      属性值       |                             说明                             |
| :---------------: | :----------------------------------------------------------: |
| default（默认值） | 由<bean>的上级标签<beans>的 default-autowire 属性值确定。例如<beans>default-autowire="byName">，则该<bean>元素中的 autowire 属性对应的属性值就为byName。 |
|      byNamee      | 根据属性的名称自动装配。容器将根据名称查找与属性完全一致的Bean，并将其属性自动装配。 |
|      byTypee      | 根据属性的数据类型（Type）自动装配，如果一个Bean的数据类型，兼容另一个Bean中属性的数据类型，则自动装配。 |
|    constructor    |    根据构造函数参数的数据类型，进行byType模式的自动装配。    |
|        no         | 默认情况下，不使用自动装配，Bean 依赖必须通过 ref元素定义。  |


自动装配一定要有 setter 方法

分别创建类A、B

```java
package com.ssm.autoware;

public class A {
    private B b;

    public void setB(B b) {
        this.b = b;
    }

    void a(){
        b.b();
        System.out.println("a");
    }
}
```

```java
package com.ssm.autoware;

public class B {
    void b(){
        System.out.println("b");
    }
}
```

填写 xml 装配文件

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="b" class="com.ssm.autoware.B"/>
    <bean id="a" class="com.ssm.autoware.A" autowire="byName"/>
</beans>
```

最后通过 Test 文件测试

```java
package com.ssm.autoware;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
        ApplicationContext applicationContext= new ClassPathXmlApplicationContext("beans7.xml");
        A a = (A) applicationContext.getBean("a");
        a.a();
    }
}
```

### 2.5作用域

|    作用域名称     |                             说明                             |
| :---------------: | :----------------------------------------------------------: |
| singleton（单例） | 使用singleton定义的Bean在Spring容器中将只有一个实例，也就是说，无论有多少个Bean引用它，始终将指同一个对象。这也是Spring容器默认的作用域。 |
| prototype（原型） | 每次通过 Spring 容器获取的 prototype 定义的 Bean 时，容器都将建一个新的 Bean实例。 |
|     requesto      | 在一次 HTTP 请求中，容器会返回该 Bean 的同一个实例。对不同的 HTTP 请求则会产生一个新的 Bean，而且该 Bean 仅在当前HTTP Request内有效。 |
|     sessione      | 在一次 HTTP Session 中，容器会返回该 Bean 的同一个实例。对不同的 HTTP 请求则会产生一个新的 Bean，且该 Bean 仅在当前 HTTP Session 内有效。 |
|  globalSessione   | 在一个全局的 HTTP Session 中，容器会返回该 Bean 的同一个实例。仅在使用portlet上下文时有效。 |
|   applicatione    | 为每个 ServletContext 对象创建一个实例。仅在 Web 相关的ApplicationContext中生效。 |
|     websocket     | 为每个  websocket对象创建一个实例。仅在 Web 相关的ApplicationContext中生效。 |



### 2.8生命周期

了解Spring中Bean生命周期有何意义？

了解Spring中Bean的生命周期的意义就在于，可以利用Bean在其存活期间的特定时刻完成一些相关操作。这种时刻可能有很多，但一般情况下，常会在Bean的postinitiation(初始化后)和predestruction（销毁前）执行一些相关操作。

|                 |                                                              |
| :-------------: | :----------------------------------------------------------: |
| singleton作用域 | Spring容器可以管理singleton作用域的Bean的生命周期在此作用域下，Spring能够精确的知道该Bean何时被创建，何时初始化完成，以及何时被销毁。 |
| prototype作用域 | prototype作用域的Bean,Spring只负责创建，当容器创建了Bean实例后，Bean的实例就交给客户端代码来管理，Spring容器将不再跟踪其生命周期。 |

