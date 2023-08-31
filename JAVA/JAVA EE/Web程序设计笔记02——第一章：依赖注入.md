# 第一章：Spring的基本应用

## 四、依赖注入

依赖注入（Dependency Injection）建成DI，与控制反转（IoC）的含义相同

依赖注入的作用就是在使用Spring 框架创建对象时，动态地将其所依赖的对象注入Bean组件中，其实现方式通常有两种，一种是属性setter方法注入，另一种是构造方法注入，具体介绍如下:

属性 setter方法注入：指Spring容器使用setter方法注入被依赖的实例。通过调用无参构造器或无参静态工厂方法实例化 Bean后，调用该Bean的setter 方法，即可实现基于 setter方法的依赖注入。

构造方法注入：指Spring容器使用构造方法注入被依赖的实例。基于构造方法的依赖注入通过调用带参数的构造方法来实现，每个参数代表着一个依赖。



在com.gzh的包下创建UserService接口并创建一个say方法，代码如下：

```java
public interface UserService {
    public void say();
}
```

在com.gzh的包下创建UserServiceImpl实现类，代码如下：

```java
public class UserServiceImpl implements UserService{

    //声明UserDao的属性
    private UserDao userDao;

    //添加UserDao属性的setter方法，用于实现依赖注入
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    //实现接口中的方法
    public void say(){
        //调用userDao中的say()方法，并执行输出语句
        this.userDao.say();
        System.out.println("UserService say Hellow World!");
    }
}
```

在applicationContext.xml里面创建一个id为userService的实例

```java
<bean id="userService" class="com.gzh.UserServiceImpl">
<!--    将id为userDao的Bean实例注入到userService的实例中-->
        <property name="userDao" ref="userDao"/>
</bean>
```

在com.gzh下创建TestDI实例

```java
public class TsetDI {
    public static void main(String[] args) {
        //1.初始化Spring容器，加载配置文件
        ApplicationContext applicationContext =new ClassPathXmlApplicationContext("applicationContext.xml");

        //2.通过容器获取UserService实例
        UserService userService =(UserService) applicationContext.getBean("userService");

        //3.调用实例中的say()方法
        userService.say();
    }
}
```

![image-20220303222001817](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220303222001817.png)

## 1.IOC（控制反转）：类对象的创建由程序员转给了Spring框架

Spring框架创建类对象的步骤：

（1）导包5个

commons_logging

spring-context

spring-beans

spring-core

spring-expression

```java
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.3.6.RELEASE</version>
</dependency>
```

（2）resources文件夹下创建applicationContext.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="userDao" class="com.gzh.UserDaoImpl"/>
```

（3）获取Spring框架创建的类对象

```java
 //使用Spring框架创建对象调用方法
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
UserDao userDao = (UserDao) applicationContext.getBean("userDao");
userDao.say();
```

## 2.DI：依赖注入

D：依赖（动词）

```java
class B{}
class A{
	B b;
	void a(){
		b.b();
	}
}
//类的对象作为A类的成员变量，就是A依赖B。
```

I：注入：就是给成员变量赋值

Spring 框架如何实现依赖注入？

直接在配置文件中写如下代码：

```java
<bean id="userService" class="com.gzh.UserServiceImpl">
<!--    将id为userDao的Bean实例注入到userService的实例中-->
        <property name="userDao" ref="userDao"/>
    </bean>
    //name属性值是成员变量，ref属性值是id属性的值
```

# 