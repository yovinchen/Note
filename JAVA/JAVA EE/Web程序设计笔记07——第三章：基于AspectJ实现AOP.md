# 第三章：Spring AOP

## 复习知识

### 1.从java角度出发实现代理

JDK动态代理 和   CGLIB代理 的区别：
（1）被代理对象不同
（2）产生代理对象的类不同

### 2.从Spring角度出发实现代理：

（1）导包：7个包：  5个核心包：commons-logging  spring-context  spring-beans  spring-core  spring-expression
                 2个包：spring-aop    aoplliance
（2）创建被代理对象，实现了某个接口的类
（3）切面类实现接口
（4）产生代理对象方式：xml文件
（5）测试

### 3.AOP术语：7个

切面类：MyAspect
连接点: addUser()  deleteUser()  调用前或后
切入点：就是连接点
增强处理／通知：就是切面中的方法   check_permission()  log()
目标对象:  被代理对象
代理：代理对象
织入：生成代理对象的过程

### 4.1和2中实现的代理有一个共同的特点（缺点）：

代理对象不管调用哪一个目标方法，都会切入切面类中的方法

### 5.有没有一种手段：让代理对象自由地选择调用哪一个目标方法时才切入切面类中的方法？

——————————————————————————————————————————————————

## 基于AspectJ实现AOP（实现代理）

### 一、基于xml

(1)导包：5个核心包：commons-logging  spring-context  spring-core  spring-beans   spring-expression
        2个包：spring-aspects    aspectjweaver
(2) 创建被代理对象
(3) 创建切面类
(4) 创建代理对象：xml文件使用aop编程
(5) 测试

代码实现如下：

```java
package com.aspectj.xml;

public interface UserDao {
    void addUser();
    void deleteUser();
}
```

```java
package com.aspectj.xml;

public class UserDaoImpl implements UserDao {

    @Override
    public void addUser() {
        System.out.println("添加用户");
//        模拟异常
//        System.out.println(1/0);
    }

    @Override
    public void deleteUser() {
        System.out.println("删除用户");
    }
}
```

```java
package com.aspectj.xml;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;

public class MyAspect {
    //前置通知
    public void myBefore(JoinPoint joinPoint){
        System.out.print("前置通知：模拟执行权限检查...,");
        System.out.print("目标类是："+joinPoint.getTarget());
        System.out.println(",被植入增强处理的目标方法为："+joinPoint.getSignature().getName());
    }
    //后置通知
    public void myAfterRturning(JoinPoint joinPoint) {
        System.out.print("后置通知：模拟记录日志...");
        System.out.println("被植入增强处理的目标方法为：" + joinPoint.getSignature().getName());
    }

//    环绕通知
//    ProceedingJoinPoint 是 JoinPoint 子接口，表示可以执行目标方法
//    1.必须是 Object 类型的返回值
//    2.必须接收一个参数，类型为 ProceeddingJoinPoint
//    3.必须 throws Throwable

    public Object myAround(ProceedingJoinPoint proceedingJoinPoint)throws Throwable{
        //开始
        System.out.println("环绕开始：执行目标方法之前，模拟开始事务...");
//        执行当前目标方法
        Object obj = proceedingJoinPoint.proceed();
        //结束
        System.out.println("环绕结束：执行目标方法之后，模拟关闭事物...");
        return obj;
    }

    //异常通知
    public void myAfterThrowing(JoinPoint joinPoint,Throwable e){
        System.out.println("异常通知："+"出错了"+e.getMessage());
    }

    //最终通知
    public void myAfter(){
        System.out.println("最终通知：模拟方法结束后的释放资源...");
    }
}
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">
<!--    1.目标对象：被代理对象-->
    <bean id="userDao" class="com.aspectj.xml.UserDaoImpl"/>
<!--    2.切面类对象-->
    <bean id="myAspect" class="com.aspectj.xml.MyAspect"/>
<!--    3.产生代理对象的aop编程-->
    <aop:config>
<!--    配置切面    -->
        <aop:aspect ref="myAspect">
<!--            1. *目标方法返回值-->
<!--            2. com.aspectj.xml 包-->
<!--            3. * 包下所有类-->
<!--            4. * 类中所有方法-->
<!--            5. （..）类中所有方法-->
<!--            配置切入点-->
            <aop:pointcut id="myPointCut" expression="execution(* com.aspectj.xml.*.*(..))"/>
<!--            配置前置通知-->
            <aop:before method="myBefore" pointcut-ref="myPointCut"/>
<!--            配置后置通知：目标方法被成功执行后，才调用的方法-->
            <aop:after-returning method="myAfterRturning" pointcut-ref="myPointCut"/>
<!--            配置环绕通知：目标方法调用前会被调用，目标方法被成功执行后，还会被调用-->
            <aop:around method="myAround" pointcut-ref="myPointCut"/>
<!--            配置异常通知：调用目标方法时发生异常才会被调用-->
            <aop:after-throwing method="myAfterThrowing" pointcut-ref="myPointCut" throwing="e"/>
<!--            配置最终通知：目标方法被调用后，不管是否被成功调用，此方法都会被调用-->
        </aop:aspect>
    </aop:config>
</beans>
```

```java
package com.aspectj.xml;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class XmlTest {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("a.xml");
        UserDao userDao = (UserDao) applicationContext.getBean("userDao");
        userDao.addUser();
        System.out.println("_______________");
        userDao.deleteUser();
    }
}
```

正常运行结果：

![image-20220328214914512](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220328214914512.png)

运行发生异常截图：出现结果为零异常

![image-20220328214954611](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220328214954611.png)

### 二、基于注解（Annotation）-------减少xml中的代码量

(1) 导包 5个核心包：commons-logging  spring-context  spring-core  spring-beans   spring-expression
        2个包：spring-aspects    aspectjweaver
(2) 创建被代理对象
(3) 创建切面类  注意：切面类中的注解相当于a.xml文件中的哪一句代码
(4) 创建xml文件：扫描所有的注解和开启AspectJ实现AOP

```java
package com.aspectj.annotation;

public interface UserDao {
    void addUser();
    void deleteUser();
}
```

```java
package com.aspectj.annotation;

import org.springframework.stereotype.Repository;

//注解实现
//@Repository("userDao")
public class UserDaoImpl implements UserDao {

    @Override
    public void addUser() {
        System.out.println("添加用户");
//        模拟异常
//        System.out.println(1/0);
    }

    @Override
    public void deleteUser() {
        System.out.println("删除用户");
    }
}
```

```java
package com.aspectj.annotation;


import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

//切面类，在此类中编写通知
@Aspect
@Component
public class MyAspect {
//    定义切入点表达式
    @Pointcut("execution(* com.aspectj.annotation.*.*(..))")
//    使用一个返回值为 void 、方法体为空的方法来命名切入点
    private void myPointCut(){}
//    前置通知
    @Before("myPointCut()")
    public void myBefore(JoinPoint joinPoint){
        System.out.println("前置通知：模拟执行权限检查...,");
        System.out.print("目标类是："+joinPoint.getTarget());
        System.out.println(",被植入增强处理的目标方法为："+joinPoint.getSignature().getName());
    }
    //后置通知
    @AfterReturning("myPointCut()")
    public void myAfterReturning(JoinPoint joinPoint){
        System.out.println("后置通知：模拟记录日志...");
        System.out.println("被植入增强处理的目标方法为：" + joinPoint.getSignature().getName());
    }
    //环绕通知
    @Around("myPointCut()")
    public Object myAround(ProceedingJoinPoint proceedingJoinPoint)throws Throwable{
        //开始
        System.out.println("环绕开始：执行目标方法之前，模拟开始事务...");
        //执行当前目标方法
        Object obj = proceedingJoinPoint.proceed();
        //结束
        System.out.println("环绕结束：执行目标方法之后，模拟关闭事物...");
        return obj ;
    }
    //异常通知
    @AfterThrowing(value = "myPointCut()",throwing = "e")
    public void myAfterThrowing(JoinPoint joinPoint,Throwable e){
        System.out.println("异常通知："+"出错了"+e.getMessage());
    }

    //最终通知
    @After("myPointCut()")
    public void myAfter(){
        System.out.println("最终通知：模拟方法结束后的释放资源...");
    }
}
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

       xmlns:context="http://www.springframework.org/schema/context"

       xmlns:aop="http://www.springframework.org/schema/aop"

       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd

       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd

       http://www.springframework.org/schema/aop
       http://www.springframework.org/schema/aop/spring-aop.xsd">
<!--    通过Bean实现-->
    <bean id="userDao" class="com.aspectj.annotation.UserDaoImpl"/>
<!--扫描所有注解-->
    <context:component-scan base-package="com.aspectj.annotation"/>
<!--    开始Aspectj实现AOP-->
    <aop:aspectj-autoproxy/>
</beans>
```

```java
package com.aspectj.annotation;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AnnotationTest {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("b.xml");
        UserDao userDao = (UserDao) applicationContext.getBean("userDao");
        userDao.deleteUser();
        userDao.addUser();
    }
}
```

运行截图：

![image-20220328215304530](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220328215304530.png)