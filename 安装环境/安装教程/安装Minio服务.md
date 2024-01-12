## MinIO是什么？

![](https://lsky.hhdxw.top/imghub/2024/01/image-202401101704861743.png)

官方解释：MinIO 是一个基于Apache License v2.0开源协议的[对象存储](https://cloud.tencent.com/product/cos?from_column=20065&from=20065)服务。它兼容亚马逊S3云存储服务接口，非常适合于存储大容量非结构化的数据，例如图片、视频、日志文件、备份数据和容器/虚拟机镜像等，而一个对象文件可以是任意大小，从几kb到最大5T不等。

MinIO是一个非常轻量的服务,可以很简单的和其他应用的结合，类似 NodeJS, Redis 或者 MySQL。

## MinIO有哪些优势

### 1. 开发文档全面

MinIO作为一款基于Golang 编程语言开发的一款高性能的分布式式存储方案的开源项目，有十分完善的官方文档。。

官网文档地址：https://docs.min.io/cn/

### 2.高性能

MinIO号称是目前速度最快的对象存储服务器。在标准硬件上，对象存储的读/写速度最高可以高达183 GB/s和171 GB/s。对象存储可以作为主存储层，用来处理Spark、Presto、TensorFlow、H2O.ai等各种复杂工作负载以及成为Hadoop HDFS的替代品。

MinIO用作云原生应用程序的主要存储，和传统对象存储相比，云原生应用程序需要更高的吞吐量和更低的延迟。而这些都是MinIO能够达成的性能指标。

### 3.容器化集成方便

MinIO提供了与k8s、etcd、docker等主流容器化技术深度集成方案。

## 安装MinIo服务

想要安装Minio有两种方案，第一种是直接在服务器上安装，还有一种是使用Docker快速部署。

官方文档：https://www.minio.org.cn/docs/cn/minio

### 直接安装

安装包下载页面：https://dl.min.io/server/minio/release/，我们需要选择好对应架构和系统的安装包，比如Ubuntu/Debian系统下，我们可以像这样安装：

```sh
wget https://dl.min.io/server/minio/release/linux-amd64/minio_20230831153116.0.0_amd64.deb -O minio.deb
sudo dpkg -i minio.deb
```

在CentOS/Redhat(RHEL)系统下，可以像这样安装：

```sh
wget https://dl.min.io/server/minio/release/linux-amd64/minio-20230831153116.0.0.x86_64.rpm -O minio.rpm
sudo dnf install minio.rpm
```

安装完成后，我们需要创建一个用于存放minio文件数据的目录：

```sh
sudo mkdir /mnt/minio
```

然后创建一个minio使用的用户，并为此用户分配对应目录的访问权限：

```sh
groupadd -r minio-user
useradd -M -r -g minio-user minio-user
chown minio-user:minio-user /mnt/minio
```

接着我们需要创建Minio的配置文件：

```sh
sudo vim /etc/default/minio
```

编写以下内容到配置文件中：

```properties
#系统管理员账号和密码
MINIO_ROOT_USER=minio
MINIO_ROOT_PASSWORD=password
#设置用于存储数据的目录
MINIO_VOLUMES="/mnt/minio"
#项目接口访问地址
MINIO_SERVER_URL="http://0.0.0.0:9000"
#配置其他选项，比如后台管理系统的地址
MINIO_OPTS="--console-address 0.0.0.0:9090"
```

配置完成之后就可以启动Minio服务了：

```sh
sudo systemctl enable minio.service
sudo systemctl start minio.service
```

### 使用Docker安装

对于各位小伙伴本地开发测试，这里更推荐使用Docker镜像安装，非常简单快捷，本地安装好Docker客户端之后，只需要执行以下命令就能快速部署：

```sh
docker run -p 9000:9000 -p 9090:9090 --name minio \
     -d --restart=always \
     -e "MINIO_ACCESS_KEY=minio" \
     -e "MINIO_SECRET_KEY=password" \
     -v database:/data \
     minio/minio server \
     /data --console-address ":9090" -address ":9000"
```

非常方便快捷。

------

## 使用Java操作对象存储

前面我们搭建好了对象存储服务，接着就可以开始愉快地使用了。

### 基本使用

Minio已经为我们提供了封装好的依赖，我们只需要直接使用即可：

```xml
<dependency>
     <groupId>io.minio</groupId>
     <artifactId>minio</artifactId>
     <version>8.3.9</version>
</dependency>
```

接着我们需要创建一个Minio客户端用于访问对象存储服务器：

```java
MinioClient client = MinioClient.builder()
        .endpoint("http://localhost:9000")  //对象存储服务地址，注意是9000那个端口
        .credentials("minio", "password")   //账户直接使用管理员
        .build();
```

对于Java的相关操作，可以说非常全面，包含对桶的操作、对象操作等等，文档地址：https://www.minio.org.cn/docs/cn/minio/linux/developers/java/API.html，这里我们先演示一下文件上传：

```java
//首先找到我们需要上传的文件
File file = new File("/Users/nagocoler/Downloads/test.jpg");
//构造一个输入流用于文件传输
FileInputStream stream = new FileInputStream(file);
//填写上传的相关参数，使用PutObjectArgs的Builder来完成
PutObjectArgs put = PutObjectArgs.builder()
        .bucket("test")   //设置需要上传的桶名称
        .object("666.jpg")  //设置上传后保存的文件名称
        .stream(stream, file.length(), -1)  //配置流以及大小还有分片大小
        .build();
client.putObject(put);   //使用putObject方法完成上传操作
```

这样就可以将文件上传到对象存储服务器中了。

接着是下载文件：

```java
//填写下载相关的参数，使用GetObjectArgs来完成
GetObjectArgs get = GetObjectArgs.builder()
        .bucket("test")   //设置需要下载的桶名称
        .object("666.jpg")  //设置下载后保存的文件名称
        .build();
GetObjectResponse response = client.getObject(get);
//使用输出流保存下载的文件到本地
FileOutputStream stream = new FileOutputStream("/Users/nagocoler/Downloads/download.png");
stream.write(response.readAllBytes());
stream.close();
```

最后是删除桶里的文件：

```java
RemoveObjectArgs remove = RemoveObjectArgs.builder()
        .bucket("test")   //设置需要删除文件的桶名称
        .object("666.jpg")   //设置删除的文件名称
        .build();
client.removeObject(remove);  //删就完事
```

### 服务器文件上传和下载

实例代码：

```java
@RestController
@RequestMapping("/")
public class FileController {

    @Resource
    MinioClient client;

    @PostMapping("upload")
    public String upload(@RequestParam("file") MultipartFile file) throws Exception {
        InputStream stream = file.getInputStream();
        String name = UUID.randomUUID().toString();
        PutObjectArgs args = PutObjectArgs.builder()
                .bucket("test")
                .object("upload/"+name)
                .stream(stream, file.getSize(), -1)
                .build();
        client.putObject(args);
        return name;
    }

    @GetMapping("file/{name}")
    public void file(@PathVariable("name") String name,
                     HttpServletResponse response) throws Exception{
        GetObjectResponse object = client.getObject(GetObjectArgs.builder().bucket("test").object("upload/"+name).build());
        ServletOutputStream stream = response.getOutputStream();
        stream.write(object.readAllBytes());
    }
}
```