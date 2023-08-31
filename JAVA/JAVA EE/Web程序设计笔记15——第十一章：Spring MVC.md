# 第十一章：Spring MVC

第一个spring应用

## 0.创建项目，导包

```java
FirstController 类的包 实现HttpServletResponse HttpServletRequest两个方法
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>servlet-api</artifactId>
  <version>2.5</version>
</dependency>
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-web</artifactId>
  <version>4.3.6.RELEASE</version>
</dependency>
    <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>4.3.6.RELEASE</version>
</dependency>
```

### 1.main->webapp->WEB-INF->web.xml文件中配置前端控制器

> DispatcherServlet:就是一个Servlet
>
> 配置前端控制器就是给DispatcherServlet配置一个访问的url



如何给一个java类配置访问它的url呢？

```java
<servlet>
    <servlet-name>逻辑名</servlet-name>
    <servlet-class>Java类的全类名（物理名）</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>逻辑名</servlet-name>
    <url-pattern>url</url-pattern>
</servlet-mapping>
```

> 补充：
>
> Java Web中有一个概念：Servlet:是什么？本质就是Java类，但是此类是通过url来访问的

```java
<servlet>
    <servlet-name>apringmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc-config.xml</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>apringmvc</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

### 2.创建控制器 FirstController

首先在 java 下创建包 com.gzh.controller ，然后创建 FirstController 类，实现接口选择 Controller 第一个

![image-20220430213958181](https://lsky.hhdxw.top/imghub/img/image-20220430213958181.png)

```java
public class FirstController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("msg","这是我的第一个Spring MVC程序");
        modelAndView.setViewName("/WEB-INF/jsp/first.jsp");
        return modelAndView;
    }
}
```

### 3.创建first.jsp

在 WEB-INF 文件下创建 jsp 文件夹然后在下面创建一个名为 first 的 jsp 文件

然后在 body 标签中创建一个 `${msg}` 在上面的 FirstController 类中 `modelAndView.addObject("msg","这是我的第一个Spring MVC程序");`相对应

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    ${msg}
</body>
</html>
```

### 4.在resources下创建springmvc-config.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd">
<!--    name中 / 表示为根目录-->
    <bean name="/firstController" class="com.gzh.controller.FirstController"/>
<!--    处理器映射器 通过一个名字 将一个 url 和 java 类进行映射-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
<!--    适配器 去找控制器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
<!--    视图解析器 把 first.jsp 渲染到浏览器上-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"/>
</beans>
```

## 5.在 Tomcat 服务器上部署项目

首先将 Tomcat  解压到盘符下，然后在idea中添加 Tomcat  

（1）首先点击 add Configurations 添加 Tomcat  服务器

（2）点击加号添加服务器

（3）选择 Tomcat  服务区 本地服务（Local 本地 	remote 远程服务）

![image-20220430215445248](https://lsky.hhdxw.top/imghub/img/image-20220430215445248.png)

（4）点击配置服务器，选择 Tomcat  服务器的主目录

![image-20220430215956914](https://lsky.hhdxw.top/imghub/img/image-20220430215956914.png)

（5）点击部署，点击添加服务，选择工件（Artifact） 

![image-20220430220220555](https://lsky.hhdxw.top/imghub/img/image-20220430220220555.png)

（6）随便选择一个即可

![image-20220430220452741](https://lsky.hhdxw.top/imghub/img/image-20220430220452741.png)

（7）最好将 应用程序上下文 改为项目名称

![image-20220430220832300](https://lsky.hhdxw.top/imghub/img/image-20220430220832300.png)

（8）然后启用服务器就可以了

![image-20220430221340859](https://lsky.hhdxw.top/imghub/img/image-20220430221340859.png)

## 6.项目部署

在 `http://localhost:8080/c11/firstController` 链接中能够看到实现的语句

![image-20220430221617115](https://lsky.hhdxw.top/imghub/img/image-20220430221617115.png)

项目根目录：`http://localhost:8080/c11/`

在上面中的  `/`  表示 `http://localhost:8080/c11/`

`/firstController`    就代表    `http://localhost:8080/c11/firstController`



![image-20220430221939788](https://lsky.hhdxw.top/imghub/img/image-20220430221939788.png)

（1）用户通过客户端向服务器发送请求，请求会被Spring MVC的前端控制器DispatcherServlet所拦截。

（2）DispatcherServlet拦截到请求后，会调用HandlerMapping处理器映射器。

（3）处理器映射器根据请求URL找到具体的处理器，生成处理器对象及处理器拦截器（如果有则生成）一并返回给DispatcherServlet。

（4）DispatcherServlet会通过返回信息选择合适的HandlerAdapter（处理器适配器）。

（5）HandlerAdapter会调用并执行Handler（处理器），这里的处理器指的就是程序中编写的Controller类，也被称之为后端控制器。

（6）Controller执行完成后，会返回一个ModelAndView对象，该对象中会包含视图名或包含模型和视图名。

（7）HandlerAdapter将ModelAndView对象返回给DispatcherServlet。

（8）DispatcherServlet会根据ModelAndView对象选择-个合适的ViewResolver（视图解析器）。

（9）ViewResolver解析后，会向DispatcherServlet中返回具体的View（视图）。

（10）DispatcherServlet对View进行渲染（即将模型数据填充至视图中）。

（11）视图渲染结果会返回给客户端浏览器显示。