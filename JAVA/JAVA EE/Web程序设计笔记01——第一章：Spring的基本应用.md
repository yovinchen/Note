# 2021版：

## Spring的基本应用

### 一、idea创建maven项目的过程

#### 1.设置idea打开时欢迎页面

1.在idea的欢迎页面中点击 Customize 进入自定义设置

2.点击 settings 进入系统设置



![image-20220301163353065](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301163353065.png)



3.在 settings 中搜索System Settings 并点击

4.将 Project 中的 Reopen projects on satrtup 对勾取消

5.将下面的 NEW windows 勾选上



![image-20220301163856266](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301163856266.png)



即可完成设置欢迎页面。

#### 2.idea中创建Maven项目的过程

1.打开欢迎页面中的设置（同上面设置）

2.再设置中搜索 Maven 并进入

3.按照图中箭头顺序2更换 apache-maven -3.6.3路径（最好将下载的maven文件解压到除C盘以外的根目录，文件可以在群里下载也可点击此下载）

4.根据箭头3重写 User settings filer 和 Local repository 的路径（官网下载的maven无repository文件夹，需要自己新建并重命名，更改成和照片相同位置即可）点击OK，完成设置。



![image-20220301164904720](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301164904720.png)



5.修改文件maven—conf—setting.xml，用记事本打开文件setting.xml，在其中搜索mirrors节点，找到如图代码区域其中蓝色部分是已经添加的代码，（添加阿里云下载镜像）（注意要添加在<mirrors>和</mirrors>两个标签之间，其它配置同理）代码如下：

```java
<mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>
   </mirror>
```



![image-20220302110623820](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220302110623820.png)



6.搜索 localRepository 将其中路径改为自己仓库位置（蓝色部分，官网下载的maven无repository文件夹，需要自己新建并重命名），保存完成修改。



![image-20220301170612258](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301170612258.png)



7.创建maven项目的过程，按照顺序依次选择点击最后next结束



![image-20220301171039307](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301171039307.png)



8.其中1是项目名称c01，2.是项目存储路径，3.Groupld是项目名com.gzh(自己名字缩写)，4.是版本号不用修改



![image-20220302110722790](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220302110722790.png)



9.这里应该是之前设置的默认项(如果不是如图所示应该重复上面的步骤进行maven配置)，点击finish完成创建。



![image-20220301171713956](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301171713956.png)



10.进入后会自动安装maven的文件，出现如图所示即安装成功



![image-20220301172210713](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301172210713.png)

### 二、Spring的体系结构

#### 1、核心模块(CoreContainer)

Spring核心模块包含有Core、Beans、Context和Expression Language四个小模块。其中，Core和Beans是整个Spring框架基础部分，也是Spring的核心依赖注入loC与DI的最基本实现，Spring的其他模块大多依赖这两个功能。

spring-core：其他模块的基础核心，包含Spring框架的核心工具类，Spring其他模块都要使用该包里面的类。

spring-beans:Spring定义bean的支持，负责访问配置文件、创建和管理bean，支持依赖注入和控制反转的相关操作。传说中的bean工厂类就在这个jar包中。

spring-context:spring运行时容器，在Core和Beans的基础上，提供对Spring的上下文支持，ApplicationContext是该包的关键，通过它，可以方便快捷的取出依赖注入的Bean。

spring-expression:spring表达式语言，帮助Spring在运行时查询和操作对象。支持设置获取对象的属性值，方法的调用

#### 2、AOP模块

spring-aop：对于代理AOP的支持

spring-Aspects：对于AspectJ的AOP支持

#### 3、Web模块

spring-web：提供基础的web功能，在Web项目中提供Spring的容器

spring-webmvc：提供基于Servlet的SpringMVC

Spring-WebSocket：提供WebSocket功能

spring-webmvc-portlet:提供portlet的支持

#### 4：数据库模块

spring-jdbc:提供jdbc访问数据库的支持，包含Srping对数据库访问操作进行封装的所有类，它提供了一个DBC的抽象层，从而实现对其他厂商的支持。 

spring-tx：提供对事物的支持 

spring-orm:提供对象关系-映射的支持，使得Spring可以方便的整合 他第三方ORM库如JAP、Mybatis、Hibernate等

spring-oxm：提供对象xml映射支持

spring-jms：提供对java消息服务的支持

### 三、Spring的入门程序

#### 1.基于Java完成接口、实现类、方法的调用。

test：

```java
项目分层：
控制层——调用服务层（用户操作控制层，发送url请求到控制层）
服务层——调用Dao层
Dao层——和数据库打交道
```

1.在 src->main 下创建两个新文件夹并重命名为 Java 和 resources 



![image-20220301180826354](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301180826354.png)



2.右击名为 Java 的文件选择 Mark Directory as 属性中的 Sources Root （源根）



![image-20220301223812386](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301223812386.png)



3.右击名为 resources 的文件选择 Mark Directory as 属性中的 Resources Root （源根）



![image-20220301223743212](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301223743212.png)

4.在 Java 源包下创建名为 UserDao 接口、名为 UserDaoImpl 的 Java 以及Test测试类

代码分别如下

```java
// UserDao 接口
public interface UserDao {
    public void say ();
}
```

```java
// UserDaoImpl 实现类
public class UserDaoImpl implements UserDao{
    @Override
    public void say() {
        System.out.println("UserDao say...");
    }
}

```

```java
//Test 测试类
public class test {
    public static void main(String[] args) {
        //调用say方法
       UserDao userDao = new UserDaoImpl();
       userDao.say();   
    }
}
```



#### 2.使用Spring框架完成接口对象的创建及方法的调用。

1.导包,在idea中的目录中的pom.xml下找到如照片代码并添加蓝色区域代码（和<dependency></dependency>标签同一级，都应该在<dependencies></dependencies>标签中）



![image-20220301225430222](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301225430222.png)

代码如下：

```java
<dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>4.3.6.RELEASE</version>
</dependency>
```

完成导包后如图：

![image-20220301230136985](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301230136985.png)



可能有部分同学导包的时候保存代码之后未下载，可以试一试重启idea应该可以解决下载问题

2.导包完成后在resources文件夹下建立 applicationContext.xml



![image-20220301230431966](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220301230431966.png)



3.并利用Spring框架创建com.wqx.UserDaoImpl类对象userDao，代码如下：

```java
<bean id="userDao" class="com.gzh.UserDaoImpl"/>
```



![image-20220302102447695](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220302102447695.png)



#### 3.使用Spring框架创建的对象调用方法

1.在 Test 测试类下输入代码：



```java
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        UserDao userDao = (UserDao) applicationContext.getBean("userDao");
        userDao.say();
```



点击运行，完成并输出：UserDao say...



![image-20220302104836410](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220302104836410.png)



