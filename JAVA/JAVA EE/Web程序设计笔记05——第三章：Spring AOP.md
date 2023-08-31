# 第三章：Spring AOP

代理：

生活中的代理：		代购（代理对象）：卖化妆品		代理		韩国卖化妆品的那个商店（被代理对象）：卖化妆品

代理对象和被代理对象的区别？

```
代理对象可以完成被代理对象的功能，除此之外，代理对象还可以添加额外功能
```

示例：

```java
package com.gzh;
//韩国商店
public class Shop {
    void sell(){
        System.out.println("韩国商店卖化妆品100元");
    }
}

```

```java
package com.gzh;

public class Daigou {
    private Shop shop;

    public void setShop(Shop shop) {
        this.shop = shop;
    }

    void sell(){
        System.out.println("代购要收十元小费");
        shop.sell();
        System.out.println("代购卖化妆品110元");
    }
}

```

```java
package com.gzh;
//Test类模拟买化妆品的人
public class Test {
    public static void main(String[] args) {
        Daigou daigou = new Daigou();
        daigou.setShop(new Shop());
        daigou.sell();
    }
}
```

运行截图：

![image-20220323125142944](https://lsky.hhdxw.top/imghub/img/image-20220323125142944.png)

## AOP术语

AOP：面向切面编程

（1）Aspect（切面）：本质就是类，例如：MyAspect，切面类完成额外功能或非核心业务功能。

（2）Joinpoint（连接点）：目标方法的调用的前和后都可以称为连接点，目标方法在被代理类中

（3）pointcut（切入点）：就是 切入了额外功能的 连接点

（4）Advice（通知、增强处理）：就是额外功能

（5）Target Object（目标对象）：被代理对象。例如：com.jdk.UserDaoImpl

（6）Proxy(代理)：代理对象

（7）Weaving（织入）：产生代理对象的过程，例如；createProxy

## 3.2.1   JDK动态代理

代理是谁？（或者说被代理对象是谁）？是实现了接口的类：com.jdk.UserDaoImpl

额外功能所在的类？   MyAspect类

如何产生代理对象？   JdkProxy类

代理对象的额外功能是如何完成的？

```java
package com.jdk;

public interface UserDao {
    void addUser();
    void deleteUser();
}
```

```java
package com.jdk;
//被代理对象就是这个实现类，该类中的方法叫做目标方法
public class UserDaoImpl implements UserDao{
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
package com.jdk;
//额外的功能：在目标方法调用前，调用check_permission;在目标方法调用后条用log方法
public class MyAspect {
    public void check_permission(){
        System.out.println("模拟权限检查");
    }
    public void log(){
        System.out.println("模拟记录日志");
    }
}
```

```java
package com.jdk;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

//产生代理对象的类
public class JdkProxy implements InvocationHandler {
    private UserDao userDao;
    //方法的返回值是代理对象
    public Object createProxy(UserDao userDao){
        this.userDao=userDao;
        ClassLoader classLoader= JdkProxy.class.getClassLoader();
        Class[] clazz=userDao.getClass().getInterfaces();
        return Proxy.newProxyInstance(classLoader,clazz,this);
    }
    //invoke方法就是代理对象完成目标功能和额外功能的方法
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        MyAspect myAspect=new MyAspect();
        myAspect.check_permission();
        Object obj = method.invoke(userDao,args);//当程序指向到此语句时，转而调用目标方法
        myAspect.log();
        return obj;
    }
}
```

```java
package com.jdk;

public class JdkTest {
    public static void main(String[] args) {
        //被代理对象
        UserDao userDao = new UserDaoImpl();
        userDao.addUser();
        System.out.println("----------------");
        //生成代理对象
        JdkProxy jdkProxy = new JdkProxy();
        UserDao proxy = (UserDao) jdkProxy.createProxy(userDao);
        proxy.addUser();//当程序指向到此语句时，转而执行invoke方法
    }
}
```

运行截图：

![image-20220323150132535](https://lsky.hhdxw.top/imghub/img/image-20220323150132535.png)



## 3.2.2CGLIB代理

代理是谁？（或者说被代理对象是谁）？User类，有两个目标方法：addUser()   deleteUser()

额外功能所在的类？   MyAspect类

如何产生代理对象？   CglibProxy类中的方法

代理对象的额外功能是如何完成的？

```java
package com.cglib;

public class User {
    void addUser() {
        System.out.println("添加用户");
    }

    void deleteUser() {
        System.out.println("删除用户");
    }
}
```

```java
package com.cglib;

//额外的功能：在目标方法调用前，调用check_permission;在目标方法调用后条用log方法
public class MyAspect {
    public void check_permission() {
        System.out.println("模拟权限检查");
    }

    public void log() {
        System.out.println("模拟记录日志");
    }
}
```

```java
package com.cglib;

import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.MethodInterceptor;
import org.springframework.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

public class Cglibproxy implements MethodInterceptor {
    public Object createProxy(Object target) {
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(target.getClass());
        enhancer.setCallback(this);
        return enhancer.create();
    }

    @Override
    public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        MyAspect myAspect = new MyAspect();
        myAspect.check_permission();
        Object obj = methodProxy.invokeSuper(proxy, args);//程序执行到此条语句时，转而执行目标方法
        myAspect.log();
        return obj;
    }
}
```

```java
package com.cglib;

public class cglibTest {
    public static void main(String[] args) {
        //被代理对象
        User user = new User();
        user.addUser();
        System.out.println("---------------");
        //代理对象
        Cglibproxy cglibProxy = new Cglibproxy();
        User proxy = (User) cglibProxy.createProxy(user);
        proxy.addUser();//程序执行到此条语句时，转为执行intercept方法
    }
}

```

运行截图如下：

![image-20220324113512719](https://lsky.hhdxw.top/imghub/img/image-20220324113512719.png)

JDK代理和CGLIB代理方式的缺点？

代理对象不管调用哪一种目标方法时，都会调用额外的功能。

能不能有一种手段：可以使得程序员自己自由指定那些目标被调用时，调用额外的功能呢？Spring