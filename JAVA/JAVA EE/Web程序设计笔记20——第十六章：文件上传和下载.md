# 第十六章：文件上传和下载

## 1.文件上传

### 1.创建项目，导包

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
<!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
<dependency>
  <groupId>commons-io</groupId>
  <artifactId>commons-io</artifactId>
  <version>2.5</version>
</dependency>
<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
<dependency>
  <groupId>commons-fileupload</groupId>
  <artifactId>commons-fileupload</artifactId>
  <version>1.3.2</version>
</dependency>
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>servlet-api</artifactId>
  <version>2.5</version>
</dependency>
```

### 2.web.xml

```java
<servlet>
  <servlet-name>springmvc</servlet-name>
  <servlet-class>
   org.springframework.web.servlet.DispatcherServlet
  </servlet-class>
  <init-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:springmvc-config.xml</param-value>
  </init-param>
  <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
  <servlet-name>springmvc</servlet-name>
  <url-pattern>/</url-pattern>
</servlet-mapping>
```

### 3.springmvc-config.xml

```java
<!-- 定义组件扫描器，指定需要扫描的包 -->
<context:component-scan base-package="com.gzh.controller" />
<!--配置注解驱动  -->
<mvc:annotation-driven />
<!-- 定义视图解析器 -->
<bean id="viewResolver" class=
   "org.springframework.web.servlet.view.InternalResourceViewResolver">
     <!-- 设置前缀 -->
     <property name="prefix" value="/WEB-INF/jsp/" />
     <!-- 设置后缀 -->
     <property name="suffix" value=".jsp" />
</bean>
<!-- 配置文件上传解析器 MultipartResolver -->
<bean id="multipartResolver" class=
  "org.springframework.web.multipart.commons.CommonsMultipartResolver">
         <!-- 设置请求编码格式-->
         <property name="defaultEncoding" value="UTF-8" />
</bean>
```

### 4.创建fileUpload.jsp

webapp下的jsp文件可以通过url直接访问
而WEB-INF下的js文件需要控制器中的方法返回才可以访问到

```html
<head>
    <meta http-equiv="Content-Type" content="text/html;charset=UTF-8"/>
    <title>Title</title>
    <script>
        function check() {
            var name=document.getElementById("name").value
            var file=document.getElementById("file").value
            if(name==""){
                alert("填写上传人");
                return false;
            }
            if(file.length==0 || file==""){
                alert("请选择上传文件")
                return false;
            }
            return true;
        }
    </script>
</head>
<body>
    <form action="${pageContext.request.contextPath}/fileUpload" method="post" enctype="multipart/form-data" onsubmit="return check()">
        上传人：<input type="text" id="name" name="name"/>
        请选择文件：<input type="file" id="file" name="uploadfile" multiple="multiple"/>
        <input type="submit" value="上传"/>
    </form>
</body>
```

### 5.创建success.jsp  error.jsp

```html
<body>
上传失败
</body>
```

```html
<body>
上传成功
</body>
```

![image-20220517103654553](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220517103654553.png)

### 6.创建控制器

```java
package com.gzh.controller;

/**
 * 文件上传
 */
@Controller
public class FileUploadController {
    /**
     * 执行文件上传
     */
    @RequestMapping("/fileUpload")
    public String handleFormUpload(@RequestParam("name") String name,
                                   @RequestParam("uploadfile") List<MultipartFile> uploadfile,
                                   HttpServletRequest request) {
        // 判断所上传文件是否存在
        if (!uploadfile.isEmpty() && uploadfile.size() > 0) {
            //循环输出上传的文件
            for (MultipartFile file : uploadfile) {
                // 获取上传文件的原始名称
                String originalFilename = file.getOriginalFilename();
                // 设置上传文件的保存地址目录
                String dirPath =
                        request.getSession().getServletContext().getRealPath("/upload/");
                File filePath = new File(dirPath);
                // 如果保存文件的地址不存在，就先创建目录
                if (!filePath.exists()) {
                    filePath.mkdirs();
                }
                // 使用UUID重新命名上传的文件名称(上传人_uuid_原始文件名称)
                String newFilename = name + "_" + UUID.randomUUID() +
                        "_" + originalFilename;
                try {
                    // 使用MultipartFile接口的方法完成文件上传到指定位置
                    file.transferTo(new File(dirPath + newFilename));
                } catch (Exception e) {
                    e.printStackTrace();
                    return "error";
                }
            }
            // 跳转到成功页面
            return "success";
        } else {
            return "error";
        }
    }
}
```

### 7.发布项目

![image-20220517105222969](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220517105222969.png)

![image-20220517105211357](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220517105211357.png)

![image-20220517105334825](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220517105334825.png)

## 2.文件下载

### 1.创建download.jsp

```html
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>下载页面</title>
</head>
<body>
<a href="${pageContext.request.contextPath }/download?filename=1.jpg">
    文件下载
</a>

<a href="${pageContext.request.contextPath }/download?filename=<%=URLEncoder.encode("壁纸.jpg", "UTF-8")%>">
    中文名称文件下载
</a>
</body>
```

### 2.添加控制器

下载文件的名字会产生乱码

```java
@RequestMapping("/download")
public ResponseEntity<byte[]> fileDownload(HttpServletRequest request,String filename) throws Exception {
    // 指定要下载的文件所在路径
    String path = request.getSession().getServletContext().getRealPath("/upload/");
    // 创建该文件对象
    File file = new File(path + File.separator + filename);
    // 设置响应头
    HttpHeaders headers = new HttpHeaders();
    // 通知浏览器以下载的方式打开文件
    headers.setContentDispositionFormData("attachment", filename);
    // 定义以流的形式下载返回文件数据
    headers.setContentType(MediaType.APPLICATION_OCTET_STREAM);
    // 使用Sring MVC框架的ResponseEntity对象封装返回下载数据
    return new ResponseEntity<byte[]>(FileUtils.readFileToByteArray(file),
            headers, HttpStatus.OK);
}
```

能保证下载的文件中文名字

```java
@RequestMapping("/download")
public ResponseEntity<byte[]> fileDownload(HttpServletRequest request,String filename) throws Exception {
    // 指定要下载的文件所在路径
    String path = request.getSession().getServletContext().getRealPath("/upload/");
    // 创建该文件对象
    File file = new File(path + File.separator + filename);
    // 对文件名编码，防止中文文件乱码
    filename = this.getFilename(request, filename);
    // 设置响应头
    HttpHeaders headers = new HttpHeaders();
    // 通知浏览器以下载的方式打开文件
    headers.setContentDispositionFormData("attachment", filename);
    // 定义以流的形式下载返回文件数据
    headers.setContentType(MediaType.APPLICATION_OCTET_STREAM);
    // 使用Sring MVC框架的ResponseEntity对象封装返回下载数据
    return new ResponseEntity<byte[]>(FileUtils.readFileToByteArray(file),
            headers, HttpStatus.OK);
}

/**
 * 根据浏览器的不同进行编码设置，返回编码后的文件名
 */
public String getFilename(HttpServletRequest request,String filename) throws Exception {
    // IE不同版本User-Agent中出现的关键词
    String[] IEBrowserKeyWords = {"MSIE", "Trident", "Edge"};
    // 获取请求头代理信息
    String userAgent = request.getHeader("User-Agent");
    for (String keyWord : IEBrowserKeyWords) {
        if (userAgent.contains(keyWord)) {
            //IE内核浏览器，统一为UTF-8编码显示
            return URLEncoder.encode(filename, "UTF-8");
        }
    }
    //火狐等其它浏览器统一为ISO-8859-1编码显示
    return new String(filename.getBytes("UTF-8"), "ISO-8859-1");
}
```

### 3.发布项目

用第一个控制器，下载的文件不是中文名称

![image-20220517112749837](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220517112749837.png)

用第二个控制器，下载的文件是中文名称

![image-20220517113013424](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/image-20220517113013424.png)