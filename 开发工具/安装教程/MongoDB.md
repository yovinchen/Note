# Docker 安装 Mongo

首先安装`Dcoker`没有安装可以参考这篇文章[Docker基本命令](https://blog.hhdxw.top/archives/235)

### 1、查看可用的 Mongo 版本

访间问 Mongo 镜像库地址：https://hub.docker.com

也可以用命令来查看

```bash
docker search mongo
```

### 2、拉取指定版本的 Mongo 镜像

这里用最新版

```bash
docker pull mongo:latest
```

### 3、查看本地镜像

用以下命令查看是否安装了Mongo

```bash
docker images
```

### 4、运行容器

```bash
docker run -itd --name mongo -p 27017:27017 mongo
```

- `docker run`：启动一个新的容器。
- `-d`：在后台运行容器。
- `--name mongo`：为容器命名为 `mongo`，以便在进一步操作时可以直接使用这个名称而不是 ID。
- `-p 27017:27017`：将主机的 27017 端口映射到容器内的 27017 端口。
- `mongo:latest`：使用最新版本的 Mongo 镜像。

### 5、安装成功

最后我们可以通过查看运行项

```bash
docker ps 
```

命令查看容器的运行信息

### 6、测试

使用

### 7、停止容器

```bash
docker stop mongo
```

### 8、关闭容器

```bash
docker rm mongo
```

