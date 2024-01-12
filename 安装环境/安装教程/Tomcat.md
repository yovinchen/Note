# Docker 安装 Tomcat

首先安装`Dcoker`没有安装可以参考这篇文章[Docker基本命令](https://blog.hhdxw.top/archives/235)

### 1、查看可用的 Tomcat 版本

访问 Redis 镜像库地址：https://hub.docker.com

也可以用命令来查看

```bash
docker search tomcat
```

### 2、拉取指定版本的 Tomcat 镜像,这里安装最新版

```bash
docker pull tomcat:latest
```

### 3、查看本地镜像

使用以下命令来查看是否已安装了 Tomcat:

```bash
docker images
```

### 4、运行容器

```bash
docker run -itd --name my-tomcat -p 8083:8080 tomcat:latest
```

- `docker run`：启动一个新的容器。
- `-itd`：在后台运行容器，并开启交互式终端（保持与容器的连接）。
- `--name tomcat`：为容器命名为 `my-tomcat`，以便在进一步操作时可以直接使用这个名称而不是 ID。
- `-p 8083:8080`：将主机的 8083 端口映射到容器内的 8080 端口。
- `tomcat:latest`：使用 Tomcat 的最新版本镜像。

### 5、安装成功

最后我们可以通过查看运行项

```bash
docker ps 
```

命令查看容器的运行信息

### 6、测试

```bash
docker exec -it tomcat /bin/bash
cp -r webapps.dist/* webapps
cd webapps
ls
```

### 7、停止容器

```bash
docker stop tomcat
```

### 8、关闭容器

```bash
docker rm tomcat
```

