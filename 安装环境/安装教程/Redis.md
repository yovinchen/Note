# Docker 安装 Redis

首先安装`Dcoker`没有安装可以参考这篇文章[Docker基本命令](https://blog.hhdxw.top/archives/235)

### 1、查看可用的 Redis 版本

访问 Redis 镜像库地址：https://hub.docker.com

也可以用命令来查看

```bash
docker search redis
```

### 2、拉取指定版本的 Redis 镜像,这里安装最新版

```bash
docker pull redis:latest
```

### 3、查看本地镜像

使用以下命令来查看是否已安装了 Redis:

```bash
docker images
```

### 4、运行容器

```bash
docker run -itd --name redis -p 6379:6379 redis
```

- `docker run`：启动一个新的容器。
- `-itd`：在后台运行容器，并开启交互式终端（保持与容器的连接）。
- `--name redis`：为容器命名为 `redis`，以便在进一步操作时可以直接使用这个名称而不是 ID。
- `-p 6379:6379`：将主机的 6379 端口映射到容器内的 6379 端口。
- `redis`：使用 Redis 镜像。

### 5、安装成功

最后我们可以通过查看运行项

```bash
docker ps 
```

命令查看容器的运行信息

### 6、客户端链接测试

Redis可视化工具[AnotherRedisDesktopManager](https://github.com/qishibo/AnotherRedisDesktopManager/releases)

### 7、停止容器

```bash
docker stop redis
```

### 8、关闭容器

```bash
docker rm redis
```

