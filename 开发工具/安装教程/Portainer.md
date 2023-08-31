# portainer Docker 可视化工具

首先安装`Dcoker`没有安装可以参考这篇文章[Docker基本命令](https://blog.hhdxw.top/archives/235)

### 1、查看可用的 portaioner 版本

访间问 portaioner 镜像库地址：https://hub.docker.com

也可以用命令来查看

```shell
docker search portainer
```

### 2、拉取镜像

```shell
docker pull portainer/portainer
```

### 3、查看本地镜像

```shell
docker images
```

### 4、运行镜像

```she l
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer
```

1. `-d` 表示在后台以守护进程的方式运行容器。
2. `-p 8000:8000 -p 9000:9000` 将主机的 8000 和 9000 端口分别映射到容器中的同样的端口，让主机可以通过这些端口来访问容器中运行的应用。其中，8000 端口是 Portainer 的 Web 界面端口，9000 端口是 Portainer API 的端口。
3. `--name=portainer` 指定容器的名称为 portainer，方便后面进行管理和操作。
4. `--restart=always` 指定当容器退出时自动重启容器，并且一直保持重启状态，确保容器一直运行。
5. `-v /var/run/docker.sock:/var/run/docker.sock` 把主机上的 Docker sock 文件挂载到容器的同样位置，让端口为 9000 的 Portainer API 可以连接到主机的 Docker 引擎，对 Docker 环境进行管理。
6. `portainer/portainer` 是要运行的镜像名称和版本。

该命令的作用就是在 Docker 中启动一个名为 `portainer` 的容器，然后将主机的端口 8000 和 9000 映射到容器的同样端口，让主机可以通过这些端口来访问运行在容器中的 Portainer Web 界面和 API。而 Portainer 则通过挂载主机上的 Docker sock 文件来获取对 Docker 引擎的访问权限，以便管理 Docker 环境下的容器、镜像和网络等。

### 5、安装成功

最后我们可以通过查看运行项

```bash
docker ps 
```

命令查看容器的运行信息

### 6、访问本地链接测试

[localhost:9000](http://localhost:9000/#/auth)

### 7、停止容器

```bash
docker stop portainer/portainer
```

### 8、关闭容器

```bash
docker rm portainer/portainer
```

