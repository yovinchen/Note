# 第十二章：Spring MVC 的核心类和注解

## 1.Spring MVC 配置文件的联众处理方式：

--是在 resources 文件夹下创建，此时需要在 web.xml 中通过 <init-param> 元素来加载配置文件

```java
<init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:springmvc-config.xml</param-value>
</init-param>
```

--是在 WEB-INF  下创建 xxx-servlet.xml ，此时不需要在web.xml文件中加载了，也就是删掉 init-param 元素就可以。

```java
<!--    name中 / 表示为根目录-->
    <bean name="/firstController" class="com.gzh.controller.FirstController"/>
<!--    处理器映射器 通过一个名字 将一个 url 和 java 类进行映射-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
<!--    适配器 去找控制器-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
<!--    视图解析器 把 first.jsp 渲染到浏览器上-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"/>
```

xxx是什么？

是 DispatcherServlet 的逻辑名，<servlet-name>中的文本。在这是指 springmvc

## 2.实现控制器的两种方式：

1.实现接口，重写handleRequest方法

2.使用注解 @Controlleri 可以将 ]ava 类标注成控制器

   使用注解 @RequestMapping 将url 映射到控制器的方法上

```java
@Controller
public class SecondController {
    @RequestMapping("/second")
    public ModelAndView handleRequest(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) throws Exception {
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("msg","这是我的第一个Spring MVC程序");
        modelAndView.setViewName("/WEB-INF/jsp/first.jsp");
        return modelAndView;
    }
}
```

然后在 WEB-INF 下创建 springmvc-servlet.xml 文件

```java
    <context:component-scan base-package="com.gzh.controller"/>
```

然后重新部署即可



> 使用注解比实现接口   实现的控制器   先进：
> 实现接口的控制器因为只重写了一个方法，所以只能处理一个请求。
> 但是使用注解的控制器中可以定义多个方法，也就是可以处理多个请求。





## 3.基于注解的 Spring MVC 应用

### 1.创建项目，导包：spring-web spring-webmvc

```java
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

### 2.web.xml中配置前端控制器

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
         http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd
         http://java.sun.com/xml/ns/javaee "
         id="WebApp_ID" version="3.0">
  <!--1.配置前端控制器:设置访问某类的url-->
  <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
</web-app>
```

### 3.使用注解的方式实现控制器

```java
@Controller
public class MyController {
    @RequestMapping("/a")
    public String a(){
        return "/WEB-INF/jsp/a.jsp";
    }
    @RequestMapping("/b")
    public String b(){
        return "/WEB-INF/jsp/b.jsp";
    }
    @RequestMapping("/c")
    public String c(){
        return "/WEB-INF/jsp/c.jsp";
    }
}
```

### 4.创建前端视图

在 webapp -> WEB-INF -> 下创建名为 jsp 的文件夹，在文件夹中创建 jsp 文件，分别创建三个前端视图，a.jsp b.jsp c.jsp

### 5.创建配置文件

在 WEB-INF 文件夹下创建名为 springmvc-servlet 的 xml 文件

```java
添加扫描注解
<context:component-scan base-package="com.gzh.controller"/>
```

### 6.在tomcat服务器上部署项目

![image-20220506092448960](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220506092448960.png)

### 7.成功发布项目

![image-20220506093333560](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220506093333560.png)

## 4.定义视图解析器（配置前后缀）

### 1.修改前端控制器

```java
@Controller
public class MyController {
    @RequestMapping("/a")
    public String a(){
        return "a";
    }
    @RequestMapping("/b")
    public String b(){
        return "b";
    }
    @RequestMapping("/c")
    public String c(){
        return "c";
    }
}
```

### 2.修改配置文件 springmvc-servlet.xml

```java
在<context>下添加以下代码
    
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <property name="suffix" value=".jsp"/>
</bean>
```

### 3.重新发布项目

![image-20220506094644157](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220506094644157.png)