# 第三章

### 1.代理

生活中的代理： 代购

从Java的角度去实现代理

### 3.2.1  JDK动态代理

被代理对象：实现了接口的类   写的就是核心的业务功能

切面类：写的是非核心的业务功能

生成代理对象的类：JdkProxy

代码实现同上节课

### 3.2.2 CGLIB代理

被代理对象：某个类

切面类：

生成代理对象的类：CglibProxy

代码实现同上节课

### AOP 术语：

AOP：面向切面编程

切面类：MyAspect

连接点: addUser()  deleteUser()  调用前或后

切入点：就是连接点

增强处理／通知：就是切面中的方法   check_permission()  log()

目标对象:  被代理对象

代理：代理对象

织入：生成代理对象的过程



### 从Spring的角度去实现代理

(1)导包：
    5个核心包：commons-logging  spring-context  spring-beans  spring-core  spring-expression
    2个包：spring-aop    aoplliance
(2)创建接口和实现类
(3)创建切面类
(4)生成代理对象  xml文件
(5)测试

pom.xml文件

```java
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.3.6.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>aopalliance</groupId>
      <artifactId>aopalliance</artifactId>
      <version>1.0</version>
    </dependency>
```

```java
package com.aop;

public interface UserDao {
    void addUser();
    void deleteUser();
}
```

```java
package com.aop;

public class UserDaoImpl implements UserDao {
    @Override
    public void addUser() {
        System.out.println("添加用户");
    }

    @Override
    public void deleteUser() {
        System.out.println("删除用户");
    }
}
```

```java
package com.aop;

import org.aopalliance.intercept.MethodInterceptor;
import org.aopalliance.intercept.MethodInvocation;

public class MyAspect implements MethodInterceptor {
    void check_permission() {
        System.out.println("模拟权限检查");
    }

    void log() {
        System.out.println("模拟记录日志");
    }

    @Override
    public Object invoke(MethodInvocation methodInvocation) throws Throwable {
        check_permission();
        Object proceed = methodInvocation.proceed();//调用目标方法
        log();
        return proceed;
    }
}
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--    1.目标对象：被代理对象-->
    <bean id="userDao" class="com.aop.UserDaoImpl"/>
    <!--    2.切面类对象-->
    <bean id="myAspect" class="com.aop.MyAspect"/>
    <!--    3.生成代理对象-->
    <bean id="userDaoproxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!--        3.1指定代理实现的接口-->
        <property name="proxyInterfaces" value="com.aop.UserDao"/>
        <!--        3.2指定目标对象-->
        <property name="target" ref="userDao"/>
        <!--        3.3指定切面，植入环绕通知-->
        <property name="interceptorNames" value="myAspect"/>
        <!--        3.4指定代理方式，true：使用cglib，false（默认）：使用jdk动态代理-->
        <property name="proxyTargetClass" value="false"/>
    </bean>
</beans>
```

```java
package com.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AopTest {
    public static void main(String[] args) {
        ApplicationContext applicationContext =
                new ClassPathXmlApplicationContext("a.xml");
        //被代理对象
        UserDao userDao = (UserDao) applicationContext.getBean("userDao");
        userDao.addUser();
        System.out.println("------------------------------");
        //代理对象
        UserDao userDaoproxy = (UserDao) applicationContext.getBean("userDaoproxy");
        userDaoproxy.addUser();

    }
}
```

代码实现截图：

![image-20220324140614514](https://lsky.hhdxw.top/imghub/img/image-20220324140614514.png)



不管是从Java角度还是Spring角度实现的代理，有一个共同点（缺点）：

代理对象不管调用哪个目标方法，都会切入切面类中的方法。



有没有一种手段，可以让代理对象自由地决定调用哪个目标方法时才切入切面类中的方法？

Aspectj框架实现代理

### 补充：异常

### 1.异常类

#### （1）算术异常：

java.lang.ArithmeticException: / by zero

```java
package com.exception;

public class ExceptionTest {
    static int divide(int x,int y){
        int result=x/y;
        return result;
    }

    public static void main(String[] args){
        int divide = divide(4,0);
        System.out.println(divide);
    }
}
```

![image-20220324142225871](https://lsky.hhdxw.top/imghub/img/image-20220324142225871.png)

#### （2）空指针异常：

java.lang.NullPointerException

```java
package com.exception;
class A{
    void a(){}
}
public class ExceptionTest2 {
    static A a;

    public static void main(String[] args){
        a.a();
    }
}
```

![image-20220324143554018](https://lsky.hhdxw.top/imghub/img/image-20220324143554018.png)

### 2.处理异常

#### （1）捕获异常

​    try{}catch(){}finally{}
​    finally可以省略，catch可以有多个

```java
package com.exception;

public class ExceptionTest5 {
    static int divide(int x,int y) throws Exception{
        int result=x/y;
        return result;
    }

    public static void main(String[] args) {
        try {
            divide(4,0);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### （2）抛异常

​    throws
​    当调用了方法抛出异常的方法时，调用者可以继续抛异常，也可捕获异常

```java
package com.exception;

public class ExceptionTest4 {
    static int divide(int x,int y) throws Exception{
        int result=x/y;
        return result;
    }

    public static void main(String[] args) throws Exception {
        divide(4,0);
    }
}
```