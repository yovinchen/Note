![img](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704267879.png)

Nexus 3 是一款由 Sonatype 公司开发的仓库管理工具。它是一个用于存储、发布和管理软件组件的集中式仓库，特别适用于 Java 开发人员。Nexus 3 支持 Maven、Gradle 和其他构建工具，并提供了强大的搜索、版本控制和权限管理功能。使用 Nexus 3 可以帮助团队更好地组织和共享代码库，并提高开发效率。

## 1.Nexus 3 的安装

### 1.docker启动命令

或者可以使用Docker-Compose进行编排

linux x86-64

```shell
docker run -d -p 8081:8081 --name nexus sonatype/nexus3
```

aarch64
```shell
docker run -d -p 8081:8081 -p 8082:8082 -p 8083:8083 --name nexus klo2k/nexus3
```
### 2.访问 Nexus 3

通过访问 http://localhost:8081 进入Nexus 3 的网站

![image-20231227152301705](https://lsky.hhdxw.top/imghub/2023/12/image-202312271703661782.png)

### 3.获取密码并登录

![image-20231227154319306](https://lsky.hhdxw.top/imghub/2023/12/image-202312271703662999.png)

点击页面右上角的"Sign in"，按弹窗提示找到默认密码
- 默认账号：admin
- 默认密码：容器内的 "/nexus-data/admin.password"
- 会提示重置密码，改个记得住的密码（或者用默认密码），后面配置需要用到

在命令行中使用下面的命令进行获取密码

```shell
docker exec nexus cat /nexus-data/admin.password
```

### 4.修改密码并设置

![image-20231227152749263](https://lsky.hhdxw.top/imghub/2023/12/image-202312271703662069.png)

默认开启匿名访问

![image-20231227152900691](https://lsky.hhdxw.top/imghub/2023/12/image-202312271703662141.png)

这就基本安装完成

![image-20231227154643340](https://lsky.hhdxw.top/imghub/2023/12/image-202312271703663203.png)



## 2.内容介绍

![](https://lsky.hhdxw.top/imghub/2023/12/image-202312271703663203.png)

右侧分为四个一级菜单分别是 `Welcome` `Search` `Browse` `Upload`

- Welcome

  - 欢迎界面，展示仓库的数据

  - Total components : 总组件数量

  - Unique logins : 登录数量（30天内）

  - Peak requests per minute : 每分钟峰值请求（24小时内）

  - Peak requests per day : 每天的峰值请求（30天内）

![image-20231227172644727](https://lsky.hhdxw.top/imghub/2023/12/image-202312271703669205.png)

- Search

  - 用于在所有仓库中，搜索是否存在某个`jar`包（依赖）
  - Group（组织）：如果我们想搜索 Spring Framework 的构件，可以使用 group:org.springframework 进行查询。
  - Artifact（构件）：如果我们想搜索 Spring Framework 的 web 模块，可以使用 artifact:spring-web 进行查询。
  - Version（版本）：如果我们想搜索 Spring Framework 版本为 5.3.10.RELEASE 的构件，可以使用 version:5.3.10.RELEASE 进行查询。另外，如果想模糊匹配多个版本，可以使用通配符 ，例如 version:5.3. 可以匹配到所有 5.3.x 版本。
  - Base Version（基本版本）：如果我们想搜索 Spring Framework 的 SNAPSHOT 版本，可以使用 baseVersion:5.3.11-SNAPSHOT 进行查询。这样就可以忽略掉 SNAPSHOT 部分，只匹配基本版本号为 5.3.11 的构件。
  - Classifier（分类器）：如果我们想搜索 Spring Framework 的 Windows 版本构件，可以使用 classifier:windows 进行查询。另外，有些 Maven 项目会为不同的环境打包不同的构件，例如 dev、test、prod 等，可以使用 classifier 进行搜索。
  - Extension（扩展名）：如果我们只想搜索 jar 文件，可以使用 extension:jar 进行查询。另外，如果我们想搜索所有类型的构件，可以省略该查询条件，默认会搜索所有扩展名的文件。

![](https://lsky.hhdxw.top/imghub/2023/12/image-202312271703669229.png)


- Browse
  - 检查该nexus私服拥有多少私有的仓库
  - maven-central 是一个代理仓库，它代表了 Maven 中央仓库，它是默认的 Maven 仓库，包含了大量的开源 Java 类库和框架，可以改成国内代理，如阿里云([https://maven.aliyun.com/repository/public](https://maven.aliyun.com/repository/public))
  - maven-public 是一个托管的仓库，它允许将自己的库发布到其中，并使其可供其他人使用。
  - maven-releases 是一个托管的仓库，用于发布稳定版本的库。
  - maven-snapshots 是一个托管的仓库，用于发布开发中或者测试版的库。

![image-20231227172852084](https://lsky.hhdxw.top/imghub/2023/12/image-202312271703669332.png)


- Upload

  - 上传 jar 包到私有仓库 
  - 点击仓库后可以进行上传 jar 包

![image-20240102150816215](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704247153.png)



## 3. Jar包推送仓库

### 1.修改项目pom.xml文件配置

- 直接改造Maven项目（或者新建一个Maven项目）
    - 执行"mvn package"能打包出jar的项目就行
    - 如下在pom.xml添加nexus3地址信息
        - 与dependencies/build同级

```xml
<dependencies>...略</dependencies>
<build>...略</build>

<distributionManagement>
    <repository>
        <id>maven-releases</id>
        <name>maven-releases</name>
        <url>${这里填写从nexus页面上复制的maven-releases的url}</url>
    </repository>
    <snapshotRepository>
        <id>maven-snapshots</id>
        <name>maven-snapshots</name>
        <url>${这里填写从nexus页面上复制的maven-snapshots的url}</url>
    </snapshotRepository>
</distributionManagement>
```

![image-20240103151816700](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704266297.png)

- 推送包
    - 执行 "mvn deoloy"
    - 此时报错 "...status: 401 Unauthorized"，说明项目配置正确

### 2.修改Maven软件配置

- 打开 _${maven根目录}/conf/settings.xml_
    - 增加nexus账号密码
    - id要和在项目pom.xml中配置的一致

```xml
<servers>
    <server>
        <id>maven-releases</id>
        <username>admin</username>
        <password>${密码}</password>
    </server>
    <server>
        <id>maven-snapshots</id>
        <username>admin</username>
        <password>${密码}</password>
    </server>
</servers>
```

![image-20240103151929256](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704266369.png)

- 再次执行"mvn deploy"就不会报错了
    - 刷新nexus页面上可以看到上传的包
        - 项目版本不带"-SNAPSHOT"在"maven-releases"目录
        
        - 否则在"maven-releases"目录
        

### 3.使用deploy推送

![image-20240103152221677](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704266542.png)

![image-20240103152112596](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704266473.png)

![image-20240103152000386](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704266400.png)

## 4. 引用jar包

### 1.修改Maven软件配置

- 打开 _${maven根目录}/conf/settings.xml_
    - 启用镜像，如果有其他镜像可以注释掉
- 重启IDE，pom.xml中像线上的库一样添加dependency即可使用

```xml
<mirror>
    <id>nexus-public</id>
    <mirrorOf>*</mirrorOf>
    <name>私有仓库</name>
    <url>${这里填写从nexus页面上复制的maven-public的url}</url>
</mirror>
```

![image-20240103151929256](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704266369.png)

### 2.项目引入jar包

```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>mini-demo</artifactId>
    <version>0.0.1-20231206.141630-1</version>
</dependency>
```

- 可以看到没有刷新之前是爆红显示在本地找不到该jar包

![image-20240103152406703](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704266647.png)

- 刷新Maven后就可以看到成功引入该jar包

![image-20240103152426226](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704266666.png)

## 5.docker镜像拉取

### 1.创建一个存储库

![image-20240103160627759](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704269188.png)

- 选择如下配置
  - Type 选择 File 或者 S3（自行配置）
  - Name 可以自行设置（这里默认 docker ）
  - 然后不选择容量提示，点击确认保存

![image-20240103161409621](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704269650.png)

### 2.创建镜像仓库

![image-20240103162040214](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704270040.png)

- 仓库分三种
  - Docker (group) 是指在一个集群环境中使用 Docker。在这种情况下，多个 Docker 容器被组织成一个群组，共享资源并通过集中的管理方式进行操作。这使得容器之间可以相互通信和协调，并且可以提供高可用性和负载均衡等功能。
  - Docker (hosted) 是指在托管环境中使用 Docker。这意味着 Docker 引擎和容器管理系统（如 Docker Swarm 或 Kubernetes）被部署在第三方云服务提供商或托管服务上。这样，用户可以将自己的应用程序打包为容器，并在托管环境中运行，而无需关心底层基础设施的维护和管理。
  - Docker (proxy) 是指在 Docker 网络中使用代理服务器。代理服务器充当位于容器和外部网络之间的中间人，转发请求和响应。使用代理服务器可以提供额外的安全性、负载均衡和缓存等功能，并允许容器与外部网络进行通信，同时隔离容器内部网络。

#### 1.创建代理仓库 Docker (proxy)

![image-20240103162218913](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704270139.png)

- Docker仓库配置设置

  - Name 仓库名称，命名为 docker-registry-163

  - Online 是否接受传入，选择勾选接受传入

  - HTTP http连接器，配置为 http 使用，不配置

  - HTTPS https连接器，配置为 https 使用，不配置
    - HTTP连接器和HTTPS连接器：用于配置Docker仓库使用的协议。HTTP连接器使用HTTP协议，而HTTPS连接器使用HTTPS协议。
  
  - Allow anonymous docker pull 允许匿名 docker 拉取，默认开启
  
  - Enable Docker V1 APl 启动 Docker V1 API ，默认开启
  
  - Remote storage 远程存储，代理仓库设置为163镜像库（其他也可）
  
  - Docker Index Docker 索引，选择为Docker Hub
  
  - Use the Nexus Repository truststore 使用Nexus存储库信任存储，默认不开启
    - 默认情况下不开启，表示是否使用Nexus存储库的信任存储。开启后，Nexus会使用自己的存储库来管理信任证书。

  - Foreign Layer Caching 外层缓存，默认不启用
    - 外层缓存可以提高Docker镜像的下载速度和性能。
  
  - Maximum component age 最大构建年龄，默认1440
  
    - 如果一些镜像或组件已经存在于仓库中超过了这个时间，它们将被自动清理掉以释放空间。
  
  - Maximum metadata age 最大元数据年龄，默认1440
  
    - 仓库中存储的元数据（metadata）的最长保留时间。这些元数据包括标签（tag）、索引（index）等信息。
  
    - 限制存储空间：通过设置最大构建年龄和最大元数据年龄，可以控制仓库中存储的镜像和相关组件的数量和大小。当镜像或组件的存在时间超过设定的最大年龄时，它们将被自动清理，从而释放存储空间。
  
    - 管理版本控制：Docker 镜像和相关组件可能会随着时间的推移多次更新和构建。通过设置最大构建年龄，您可以确保仓库中只保留最新的镜像和组件，这有助于避免过时的版本占用存储空间。
  
    - 提高性能：仓库中存储的元数据（如标签、索引等）也会随着时间的推移不断增长。通过设置最大元数据年龄，可以定期清理过时的元数据，从而提高仓库的性能和响应速度。
  
    - 维护仓库健康：定期清理过时的镜像、组件和元数据有助于保持仓库的健康状态。它可以防止仓库变得杂乱不堪，减少潜在的错误和故障。
  - Blob store 存储库，选择前面设置的 docker
  - Not found cache enabled 未找到启用的缓存，默认勾选
    - 用于启用或禁用 "Not Found Cache" 功能。当启用此功能时，如果某个请求在仓库中未找到所需的内容（例如镜像或组件），仓库将会缓存这个"未找到"的结果。这样，当后续的请求再次发生时，仓库可以更快地响应，而不必重新执行相同的查询
  - Not found cache TTL 未找到缓存TTL，默认1440
    - 指 "Not Found Cache" 功能中缓存的超时时间。一旦某个未找到的结果被缓存起来，在经过一段时间后（即 TTL 时间）将会自动过期并从缓存中清除。
  - Cleanup Policies 清理政策，不做更改
    - 用于定义清理操作的策略。它允许您指定仓库中过期或不再需要的镜像、组件或其他资源的自动清理规则。可以根据各种条件（例如最后访问时间、构建日期等）来定义清理政策，以确保仓库保持干净和高效。

![image-20240103162453578](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704270293.png)

![image-20240103162505966](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704270306.png)

#### 2.创建主仓库 Docker (group)

- 配置
  - Name 为 docker-registry
  - HTTP 对外接口为 8082（前面创建时候启用8082端口）
  - 勾选允许匿名拉取
  - 勾选 Docker V1 API
  - 存储库设置为 docker
  - Member repositories 存储库设置为前面创建的 docker-registry-163

![image-20240103165831888](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704272312.png)

#### 3.启用 Docker

![image-20240103170429111](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704272669.png)

#### 4.对应服务器配置镜像仓库

- ip以及配置
  - Nexus 3 安装在了本地电脑的 docker 中，本地IP 10.211.55.2
  - 目标服务器 CentOS Stream 9 aarch64 ，IP 10.211.55.40

```sh
vim /etc/docker/daemon.json
```

在文件中写入下面的配置

```json
{
    "insecure-registries": [
    "10.211.55.2:8082"
  ],
  "registry-mirrors": [
    "http://10.211.55.2:8082"
  ]
}
```

重启 Docker

```sh
systemctl daemon-reload
systemctl restart docker
```

## 6.docker镜像推送

#### 1.nexus 3 配置

创建 docker-private 存储库

![image-20240103231907873](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704295148.png)

- 创建镜像仓库 docker(hosted)
  - HTTP 端口 8083
  - 开启匿名拉取
  - 开启 Docker V1 API 接口
  - 存储库 docker-private

![image-20240103232044665](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704295245.png)

docker-registry 仓库中增加存储库 docker-private

![image-20240103232319285](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704295399.png)

- 创建角色
  - Role ID 角色ID ，设置为 docker-publish
  - Role Name 角色名称，设置为 docker-publish
  - Role Description 角色描述，设置为 docker-publish
  - Privilegs 权限，设置为 nx-repository-admin-docker-docker-private-*
    - nx-repository-admin-docker-docker-private-* ，代表Docker 仓库中的私有仓库。这个特权表示被赋予的角色将对私有 Docker 仓库具有管理权限，包括创建、删除、推送和拉取镜像等操作。
  - Roles 角色，设置为 nx-admin

![image-20240103232509055](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704295509.png)

![image-20240103232522379](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704295522.png)

- 创建用户
  - ID ，设置为docker
  - Fisrst Name ，设置为docker
  - Last Name ，设置为docker
  - Email ，设置为docker@gmail
  - Password ，设置为docker
  - status 状态，设置为 Active
  - Roles 角色，赋予 docker-publish

![image-20240103233251898](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704295972.png)

#### 2.配置服务器

- ip以及配置
  - Nexus 3 安装在了本地电脑的 docker 中，本地IP 10.211.55.2
  - 目标服务器 CentOS Stream 9 aarch64 ，IP 10.211.55.40

```sh
vim /etc/docker/daemon.json
```

在文件中写入下面的配置

```json
{
    "insecure-registries": [
    "10.211.55.2:8082",
    "10.211.55.2:8083"
  ],
  "registry-mirrors": [
    "http://10.211.55.2:8082"
  ]
}
```

```sh
//重启 Docker
systemctl daemon-reload
systemctl restart docker

//查看本地镜像
docker images

//打标签
docker tag redis:latest 10.211.55.2:8083/redis:v1.0

//登录仓库
docker login -u docker -p docker 10.211.55.2:8083

//推送打标签的镜像
docker push 10.211.55.2:8083/redis:v1.0
```

![image-20240103234013588](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704296413.png)

在本地仓库就能看到上传的镜像了

![image-20240103234244993](https://lsky.hhdxw.top/imghub/2024/01/image-202401031704296565.png)
