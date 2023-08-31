# 第十三章：数据绑定

## 1.什么叫数据绑定

将请求消息数据   和   后台方法形参   建立连接的过程  就叫数据绑定。

请求消息数据：

		可以通过url携带数据，比如localhost:8080/c14/get?id=1
	
		还可以通过表单提交数据

后台方法形参

		控制器中方法的形参

根据后台方法形参的类型，将数据绑定分为：简单数据绑定，复杂数据绑定

## 2.简单数据绑定

### （1）默认的数据类型

> HttpServletRequest:通过request对象获取请求信息。
>
> HttpServletResponse:通过response处理响应信息。
>
> HttpSession:通过session对象得到session中存储的对象。
>
> Model//ModelMap:Model是一个接口，ModelMap是一个接口实现，作用是将model数据填充到request域。

后台方法形参的默认的数据类型有4个，以 HttpServletRequest 为例进行讲解

#### 1.创建项目，导包  spring-web   spring-webmvc

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

#### 2.web.xml 文件中配置前端控制器

```java
<!--1.配置前端控制器:设置访问某类的url-->
<servlet>
  <servlet-name>springmvc</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:springmvc-config.xml</param-value>
  </init-param>
</servlet>
<servlet-mapping>
  <servlet-name>springmvc</servlet-name>
  <url-pattern>/</url-pattern>
</servlet-mapping>
```

#### 3.在 resources 下创建 springmvc-config.xml

只创建文件

#### 4.创建控制器

```java
@Controller
public class UserController {
    @RequestMapping("/selectUser")
    public String selectUser(HttpServletRequest request) {
        String id = request.getParameter("id");
        System.out.println("id=" + id);
        return "success";
    }
}
```

#### 5.创建前端页面 success.jsp

在 WEB-INF 文件夹下面创建 jsp 文件夹，然后创建前端页面

```html
<body>
success
</body>
```

#### 6.解析注解和配置视图解析器

```java
<!--解释控制器中的注解-->
    <context:component-scan base-package="com.gzh.controller"/>
<!--配置前端视图解析器 id可以不写，不写等于class-->
    <bean  class="org.springframework.web.servlet.view.InternalResourceViewResolver">
<!--        前缀-->
        <property name="prefix" value="/WEB-INF/jsp/"/>
<!--        后缀-->
        <property name="suffix" value=".jsp"/>
    </bean>
```

#### 7.在 tomcat 服务器上部署项目，发布项目

![image-20220506220115521](https://lsky.hhdxw.top/imghub/img/image-20220506220115521.png)

#### 7.测试 发送：

```java
http://localhost:8080/c15/selectUser?id=1
```

![image-20220506220059705](https://lsky.hhdxw.top/imghub/img/image-20220506220059705.png)

### （2）简单的数据类型 int double float string

#### 1.控制器：

```java
@RequestMapping(value = "/getUser")
public String getUser(int id) {
    System.out.println("id=" + id);
    return "success";
}
```

#### 2.测试 发送

```java
http://localhost:8080/c15/getUser?id=4
```

![image-20220506221558058](https://lsky.hhdxw.top/imghub/img/image-20220506221558058.png)



### 注意：

当 **getuser()** 中的参数 **i** 要和后面的一致，在绑定时也要进行相应更改

![image-20220506222128788](https://lsky.hhdxw.top/imghub/img/image-20220506222128788.png)

添加注解 **@RequestParam** 就可以将 **id** 参数的值绑定到 **a** 上

![image-20220506222455550](https://lsky.hhdxw.top/imghub/img/image-20220506222455550.png)

### （3）POJO plain old Java object

### 以用户注册为例:

#### 1.创建实体类User

```java
private Integer id;
private String username;
private String password;
实现 getter setter 和 toString 方法
```

#### 2.创建前端页面 register.jsp

```html
<body>
<form action="http://localhost:8080/c15/registerUser" method="post">
<%--    name 属性值 和 成员变量名 保持一致--%>
    用户名：<input type="text" name="username"/>
    密码：<input type="password" name="password"/>
    <input type="submit" value="注册"/>
</form>
</body>
```

#### 3.控制器

```java
//    该方法是用来返回register.jsp页面
@RequestMapping("/toRegister")
public String toRegister() {
    return "register";
}

//    该方法是用来处理数据的
@RequestMapping("/registerUser")
public String registerUser(User user) {
    System.out.println(user.getUsername() + ":" + user.getPassword());
    return "success";
}
```

#### 4.测试 发布

![image-20220506223113983](https://lsky.hhdxw.top/imghub/img/image-20220506223113983.png)

在中文的情况下可能会发生乱码，所以添加编码过滤器，将所有文件用 utf-8 格式编码

在 WEB-INF 文件夹下的 web.xml 文件下的 前端控制器代码下 添加以下代码

```java
<!--  配置编码过滤器-->
  <filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
      <param-name>encoding</param-name>
      <param-value>UTF-8</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
```

测试 发布：中文不会产生乱码

![image-20220506223612935](https://lsky.hhdxw.top/imghub/img/image-20220506223612935.png)

### （4）包装的POJO类型

使用简单POO类型已经可以完成多数的数据绑定，但有时客户端请求中传递的参数会比较复杂。例如，在用户查询订单时，页面传递的参数可能包括订单编号、用户名称等信息，这就包含了订单和用户两个对象的信息。如果将订单和用户的所有查询条件都封装在一个简单POJO中，显然会比较混乱，这时就可以考虑使用包装POJO类型的数据绑定。

#### 1.在 com.gzh.domain 包下创建名为 Orders  的类

```java
private Integer ordersId;
private User user;
实现 getter 和 setter 方法
```

#### 2.在 com.gzh.controller 包下创建控制器类 OrdersController

编写一个跳转到订单查询页面的方法和一个查询订单及用户信息的方法

```java
@Controller
public class OrdersController {
    @RequestMapping("/toFindOrdersWithUser")
    public String toFindOrdersWithUser(){
        return "orders";
    }

    @RequestMapping("/")
    public String findOrdersWithUser(Orders orders){
        Integer ordersId = orders.getOrdersId();
        User user = orders.getUser();
        String username = user.getUsername();
        System.out.println("ordersId" + ordersId);
        System.out.println("username" + username);
        return "success";
    }
}
```

#### 3.创建前端页面 orders.jsp

在 WEB-INF 文件夹下的 jsp 文件夹下，创建前端页面 orders.jsp

```html
<body>
<form action="http://localhost:8080/c15/findOrdersWithUser" method="post">
    订单编号：<input type="text" name="ordersId" />
    所属用户：<input type="text" name="user.username" />
    <input type="submit" value="查询" />
</form>
</body>
```

#### 4.测试 发布

![image-20220507091032381](https://lsky.hhdxw.top/imghub/img/image-20220507091032381.png)

## 3.复杂的数据绑定

### （1）绑定数组

#### 1.创建前端页面 user.jsp

```html
<body>
<form method="post" action="http://localhost:8080/c15/delect">
    <table width="20%" border="1">
        <tr>
            <td>选择</td>
            <td>用户名</td>
        </tr>
        <tr>
            <td><input type="checkbox" name="ids" value="1"/></td>
            <td>tom</td>
        </tr>
        <tr>
            <td><input type="checkbox" name="ids" value="2"/></td>
            <td>jack</td>
        </tr>
    </table>
    <input type="submit" value="删除"/>
</form>
</body>
```

#### 2.编写批量删除用户的方法（同时添加一个向 user.jsp 页面跳转的方法）

```java
//    该方法是返回user.jsp的
    @RequestMapping("/toUser")
    public String toUser(){
        return "user";
    }

//    该方法是来处理user.jsp页面传递的数据
    @RequestMapping("/delect")
    public String delectUser(int[] ids){
        if (ids != null){
            for (int id : ids){
                System.out.println("删除了id为"+id+"的用户");
            }
        }else {
            System.out.println("数组ids为空");
        }
        return "success";
    }
```

#### 3.测试 发布：

![image-20220506224513695](https://lsky.hhdxw.top/imghub/img/image-20220506224513695.png)



![image-20220506224526191](https://lsky.hhdxw.top/imghub/img/image-20220506224526191.png)

### （2）绑定集合

以批量修改用户为例：

#### 1.创建包装类 UsersVo

```java
private List<User> users;
实现 getter 和 setter 方法
```

#### 2.编写控制器方法

```java
    @RequestMapping("/toUserEdit")
    public String toUserEdit() {
        return "user_edit";
    }

    @RequestMapping("/editUsers")
    public String editUsers(UserVo userList) {
//        将所有用户数据封装到集合中
        List<User> users = userList.getUsers();
//        循环输出信息
        for (User user : users) {
//            如果接收的用户id不为空，则表示对该用户进行了修改
            if (user.getId() != null) {
                System.out.println("修改了id为" + user.getId() + "的用户名为：" + user.getUsername());
            }
        }
        return "success";
    }
```

#### 3.编写前端页面 user_edit.jsp

```html
<body>
<form action="http://localhost:8080/c15/editUsers" method="post" id="formid">
    <table width="30%" border="1">
        <tr>
            <td>选择</td>
            <td>用户名</td>
        </tr>
        <tr>
            <td>
                <input name="users[0].id" value="1" type="checkbox"/>
            </td>
            <td>
                <input name="users[0].username" value="tome" type="text"/>
            </td>
        </tr>
        <tr>
            <td>
                <input name="users[1].id" value="2" type="checkbox"/>
            </td>
            <td>
                <input name="users[1].username" value="jack" type="text"/>
            </td>
        </tr>
    </table>
    <input type="submit" value="修改"/>
</form>
</body>
```

#### 4.测试发布

![image-20220507121847120](https://lsky.hhdxw.top/imghub/img/image-20220507121847120.png)