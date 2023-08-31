# 第十四章：JSON 数据和 RESTful 风格的 url

## 1.JSON: JavaSript Object Notation

> JSON数据的两种数据结构：
>
> 1.对象结构
>
> 2.数组结构

## 2.第13章和第14章的区别：

第13章：发送ur1,请求控制器中的某个方法，该方法返回jsp页面
第14章：发送url,请求webapp下的jsp页面，该页面中包含一个门S0N数据，该数据会被传递到控制器中的某个方法上，然后方法再将数据返回显示到浏览器中。

## 3.验证JS0N数据和Java类对象的转换

### 1.创建项目，导包：

```java
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
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-annotations</artifactId>
  <version>2.8.8</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-core</artifactId>
  <version>2.8.8</version>
</dependency>
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.8.8</version>
</dependency>
```

### 2.web.xml文件

```java
<!--  打开项目直接访问的页面-->
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index1.jsp</welcome-file>
  </welcome-file-list>

<!--  配置前端控制器DispatcherServlet-->
  <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
```

### 3.springmvc-servlet.xml文件

```java
<!--  定义组件扫描器：指定需要扫描的包-->
  <context:component-scan base-package="com.gzh.controller"/>
<!--  配置视图解析器-->
  <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <property name="suffix" value=".jsp"/>
  </bean>
<!--  配置 Spring MVC的注解驱动-->
  <mvc:annotation-driven/>
<!--  配置静态资源的访问映射，配置的文件，将不被前端控制器拦截-->
  <mvc:resources mapping="/js/**" location="/js/"/>
```

### 4.创建实体类对象User

```java
package com.gzh.po;

private String username;
private String password;
通过 getter setter 和 toString 方法生成代码
```

### 5.在WEB-INF下创建 index.jsp

```html
<head>
    <title>测试JSON交互</title>
    <meta http-equiv="Content-Type" content="text/html;charset=utf-8">
    <script type="text/javascript" src="${pageContext.request.contextPath}/js/jquery-1.11.3.min.js">
    </script>
    <script type="text/javascript">
        function testJson() {
            var username = $("#username").val();
            var password = $("#password").val();
            $.ajax({
                url: "${pageContext.request.contextPath}/testJson",
                type: "post",
                data: JSON.stringify({username: username, password: password}),
                contentType: "application/json;charset=utf-8",
                dataType: "json",
                success: function (data) {
                    if (data != null) {
                        alert("您输入的用户名为" + data.username + "密码为" + data.password);
                    }
                }
            });
        }
    </script>
</head>
<body>
<form>
    用户名<input type="text" name="username" id="username"><br>
    密码<input type="password" name="password" id="password"><br>
    <input type="button" value="测试JSON交互" onclick="testJson()">
</form>
</body>
```

### 6.导入文件 jquery-1.11.3.min.js

在webapp下创建文件夹 js 在文件夹下复制文件

![image-20220513233035481](https://lsky.hhdxw.top/imghub/img/image-20220513233035481.png)

### 7.创建UserController方法

```java
package com.gzh.controller;

@Controller
public class UserController {
    @RequestMapping("/testJson")
    @ResponseBody
    public User testJson(@RequestBody User user){
        System.out.println(user);
        return user;
    }
}
```

### 8.发布项目

![image-20220513231414012](https://lsky.hhdxw.top/imghub/img/image-20220513231414012.png)

## 4.RESTful支持

### 1.在 UserController 方法中添加查询方法

```java
/**
 *接收RESTful风格的请求,其接收方式为GET
 */
@RequestMapping(value="/user/{id}",method= RequestMethod.GET)
@ResponseBody
public User selectUser(@PathVariable("id") String id){
    //查看数据接收
    System.out.println("id="+id);
    User user=new User();
    //模拟根据id查询出到用户对象数据
    if(id.equals("1234")){
        user.setUsername("tom");
    }
    //返回JSON格式的数据
    return user;
}
```

### 2.编写页面文件 restful.jsp 

```html
<head>
<title>RESTful测试</title>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<script type="text/javascript" 
    src="${pageContext.request.contextPath }/js/jquery-1.11.3.min.js">
</script>
<script type="text/javascript">
function search(){
   // 获取输入的查询编号
   var id = $("#number").val();
   $.ajax({
      url : "${pageContext.request.contextPath }/user/"+id,
      type : "GET", 
      //定义回调响应的数据格式为JSON字符串,该属性可以省略
      dataType : "json",
      //成功响应的结果
      success : function(data){
         if(data.username != null){ 
             alert("您查询的用户是："+data.username);
         }else{
             alert("没有找到id为:"+id+"的用户！");
         }
      }
   });
}
</script>
</head>
<body>
    <form>
         编号：<input type="text" name="number" id="number">
       <input type="button" value="搜索" onclick="search()" />
    </form> 
</body>
```

### 3.发布项目

![image-20220513232938453](https://lsky.hhdxw.top/imghub/img/image-20220513232938453.png)