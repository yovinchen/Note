# Docker 的基本命令

## 1、简单运用

#### （1）获取 Docker 镜像

```shell
docker pull [IMAGE_NAME:TAG]
```

通过该命令，可以从 Docker Hub 或其他镜像源获取指定名称和标签的 Docker 镜像。例如，执行以下命令可以获取 Docker 官方的 Nginx 镜像

```shell
docker pull nginx:latest
```

#### （2）运行 Docker 容器

```shell
docker run [OPTIONS] IMAGE_NAME [COMMAND] [ARG...]
```

该命令用于在 Docker 中运行一个容器。其中，IMAGE_NAME 是要使用的 Docker 镜像名称，而 OPTIONS 则为可选的参数，常用的参数包括

- `-d`以守护进程方式运行容器。
- `-p`将主机端口映射到容器端口。
- `--name`为容器指定一个名称。
- `-v`挂载主机目录到容器中。
- `--restart`设置容器退出时的重启策略等。

COMMAND 和 ARG... 则为可选的命令和参数。例如，运行一个名为 myapp 的容器，使用的镜像为 myimage，80表示容器内部要映射的端口号，主机上要映射到容器内部端口的端口号。，可以执行以下命令

```shell
docker run -d -p 80:90 --name myapp myimage
```

#### （3）查看当前所有正在运行的容器

```shell
docker ps
```

该命令用于列出当前所有正在运行的 Docker 容器，包括容器 ID、名称、所使用的镜像、状态等基本信息。

如果要查看所有容器，包括已经停止的容器，可以添加 `-a` 选项

```shell
docker ps -a
```

#### （4）查看本地镜像列表

```shell
docker images
```

该命令用于列出本地已经保存的 Docker 镜像列表，包括镜像名称、标签、镜像 ID、大小等基本信息。

如果要查看某个特定镜像的详细信息，可以执行以下命令

```shell
docker images [IMAGE_NAME]
```

其中，IMAGE_NAME 为要查看信息的镜像名称。

#### （5）停止运行中的容器

```shell
docker stop [CONTAINER_ID/CONTAINER_NAME]
```

该命令用于停止指定 ID 或名称的 Docker 容器。例如，停止容器 ID 为 `1234567890ab` 的容器，可以执行以下命令

```shell
docker stop 1234567890ab
```

如果需要停止多个容器，可以依次添加多个容器的 ID 或名称，例如

```shell
docker stop container1 container2 ...
```

#### （6）强制终止运行中的容器

```shell
docker kill [CONTAINER_ID/CONTAINER_NAME]
```

该命令用于强制终止运行中的容器。与 `docker stop` 命令不同，`docker kill` 命令会直接杀死容器进程，不会给容器发送停止信号。例如，强制终止名为 myapp 的容器，可以执行以下命令

```shell
docker kill myapp
```

#### （7）删除已经停止的容器

```shell
docker rm [CONTAINER_ID/CONTAINER_NAME]
```

该命令用于删除指定 ID 或名称的停止状态 Docker 容器。例如，删除名称为 myapp 的容器，可以执行以下命令

```shell
docker rm myapp
```

如果需要批量删除已停止的容器，可以依次添加多个容器的 ID 或名称，例如

```shell
docker rm container1 container2 ...
```

#### （8）删除不再使用的镜像

```shell
docker rmi [IMAGE_ID/IMAGE_NAME]
```

该命令用于删除本地不再需要使用的 Docker 镜像。其中，IMAGE_ID/IMAGE_NAME 为要删除的镜像的 ID 或名称。例如，删除镜像 ID 为 `0123456789ab` 的 Docker 镜像，可以执行以下命令

```shell
docker rmi 0123456789ab
```

如果需要删除某个名称的所有镜像，也可以通过名称来删除镜像，例如

```shell
docker rmi myimage
```

如果要删除本地所有不再使用的镜像，可以执行以下命令

```shell
docker rmi $(docker images -f "dangling=true" -q)
```

#### （9）进入正在运行的容器

```shell
docker exec -it [CONTAINER_ID/CONTAINER_NAME] /bin/bash
```

该命令用于进入正在运行的 Docker 容器中。其中，`-it` 选项表示以交互式终端方式进入容器，`/bin/bash` 则是指定进入容器后要执行的命令。

例如，进入容器 ID 为 `0123456789ab` 的容器，可以执行以下命令

```shell
docker exec -it 0123456789ab /bin/bash
```

#### （10）查看容器日志

```shell
docker logs [CONTAINER_ID/CONTAINER_NAME]
```

该命令用于查看指定 ID 或名称的 Docker 容器的日志输出。例如，查看名称为 myapp 的容器的日志输出，可以执行以下命令

```shell
docker logs myapp
```

## 2、详细命令

### 1、镜像管理

#### （1）查看镜像

```shell
docker image is
```

```shell
docker images
```

#### （2）检索镜像

```shell
docker search [image]
```

例子

```shell
docker search negix
```

#### （3）拉取镜像

```shell
docker pull [image]
```

#### （4）上传镜像

```shell
docker push [image]
```

例子

```shell
docker push yovinchen/docker-test:latest
```

#### （5）保存镜像

```shell
docker save [image] -o FILE/
```

```shell
docker save [image] >FILE
```

例子

```shell
docker save yovinchen/docker-test:latest >docker-test.tar
```

#### （6）导入镜像

```shell
docker load -i FILE
```

例子

```shell
docker load -i hello-docker.tar
```

#### （7）查看历史镜像

```shell
docker history [image]
```

#### （8）删除镜像

```shell
docker rmi [image]
```

```shell
docker image rm [imaeg]
```

#### （9）删除不再使用的镜像

```shell
docker image prune
```

#### （10）将文件系统导入为镜像

```shell
docker import [URL/FILE]
```

#### （11）从容器创建镜像

```shell
docker commit [container][image]
```

### 2、网络管理

#### （1）列出可⽤⽹络

```shell
docker network ls
```

#### （2）查看⽹络详细信息

```shell
docker network inspect [network]
```

#### （3）创建⼀个新的⽹络

```shell
docker network create [network]
```

#### （4）删除⼀个⽹络

```shell
docker network rm [network]
```

#### （5）将容器连接到⽹络

```shell
docker network connect [network] [container]
```

#### （6）将容器从⽹络断开

```shell
docker network disconnect [network] [container]
```

### 3、插件管理

#### （1）列出插件

```shell
docker plugin ls
```

#### （2）安装插件

```shell
docker plugin install [plugin]
```
#### （3）启⽤插件

```shell
docker plugin enable [plugin]
```
#### （4）禁⽤插件

```shell
docker plugin disable [plugin]
```
#### （5）卸载插件

```shell
docker plugin rm [plugin]
```

### 4、容器管理

#### （1）创建容器（仅创建，不运行）

```shell
docker create [image]
```

#### （2）创建并运行容器

```shell
docker run [image]
```

#### （3）启动容器

```shell
docker start [container]
```

#### （4）停止容器

```shell
docker stop [container]
```

#### （5）重启容器

```shell
docker restart [container]
```

#### （6）列出正在运行的容器

```shell
docker ps
docker container ls
```

#### （7）列出所有容器

```shell
docker ps -a
```

```shell
docker container ls -a
```

#### （8）以交互模式进入容器

```shell
docker exec -it [container] bash
```

```shell
docker attach [container]
```

#### （9）导出容器快照到文件

```shell
docker export [container] -o FILE
```

```shell
docker export [container] > FILE
```

#### （10）导入容器快照

```shell
docker import FILE
```

#### （11）查看容器日志

```shell
docker logs [container]
```

#### （12）删除容器

```shell
docker rm [container]
```

```shell
docker container rm [container]
```

#### （13）查看容器端口映射

```shell
docker port [container]
```

#### （14）显示容器内进程

```shell
docker top [container]
```

#### （15）复制本地文件到容器内指定路径

```shell
docker cp [FILE] [container]:[PATH]
```

#### （16）显示容器内的变化

```shell
docker diff [container]
```

#### （17）显示容器资源使用情况

```shell
docker stats [container]
```

### 5、数据卷管理

#### （1）创建一个数据卷

```shell
docker volume create [volume]
```

#### （2）查看数据卷列表

```shell
docker volume ls
```

#### （3）查看数据卷详细信息

```shell
docker volume inspect [volume]
```

#### （4）删除数据卷

```shell
docker volume rm [volume]
```

#### （5）删除所有未使用的数据卷

```shell
docker volume prune
```

### 6、日常操作

#### （1）查看Docker系统信息

```shell
docker info
```

#### （2）查看Docker版本

```shell
docker version
```

#### （3）查看Docker帮助文档

```shell
docker --help
```

#### （4）查看Docker命令帮助

```shell
docker [command] --help
```

#### （5）登录DockerHub

```shell
docker login
```

#### （6）退出DockerHub

```shell
docker logout
```

### 7、容器运行

语法格式 

```shell
docker run [options] image [command] [arg...]
```

#### （1）创建运行并命名容器

```shell
shell复制代码docker run --name [name] [image]
```

#### （2）创建一个容器并后台运行

```shell
shell复制代码docker run -d [image]
```

#### （3）创建一个容器并指定端口映射

```shell
shell复制代码docker run -p [hostPort]:[containerPort] [image]
```

#### （4）创建一个容器并随机分配端口映射

```shell
shell复制代码docker run -P [image]
```

#### （5）创建一个容器并指定环境变量

```shell
shell复制代码docker run -e [key=value] [image]
```

#### （6）创建一个容器并指定工作目录

```shell
shell复制代码docker run -w [PATH] [image]
```

#### （7）创建一个容器并指定容器名称

```shell
shell复制代码docker run --name [name] [image]
```

#### （8）创建一个容器并在容器中执行命令（交互模式）

```shell
shell复制代码docker run [image] [command]
```

#### （9）创建一个容器，并指定容器名称、后台运行、端口映射、环境变量、工作目录

```shell
shell复制代码docker run -d -p [hostPort]:[containerPort] -e [key=value] -w [PATH] --name [name] [image]
```

#### （10）使用镜像nginx:latest来启动一个容器，并在容器内执行交互式bash shell

```shell
shell复制代码docker run -it nginx:latest /bin/bash
```

#### （11）创建一个MySQL容器，后台模式启动，将主机的80端口映射到容器的80端口，将主机的/data目录映射到容器的/data目录

```shell
shell复制代码docker run -it -p 3316:3306 -v /data:/data -d mysql:latest
```

### 8、常用Dockerfile指令

#### （1）指定基础镜像，必须为Dockerfile的第⼀条指令；

```shell
FROM [base_image]
```

#### （2）⽤于将⽂件复制到镜像中，源可以使URL或者本地⽂件，也可以⼀个压缩⽂件（⾃动解压）；

```shell
ADD [--chown=<user>:<group>] [源路径] [⽬标路径]
```

#### （3）⽤于将⽂件拷⻉到镜像中，源只能是本地⽂件；

```shell
COPY [--chown=<user>:<group>] [源路径] [⽬标路径]
```

#### （4）⽤于指定⼯作⽬录，可以使⽤多个WORKDIR指令，如果使⽤相对路径，则是相对于上⼀条WORKDIR指令所指定的⽬录；

```shell
WORKDIR [PATH]
```

#### （5）⽤于设置环境变量

```shell
ENV <key> <value>
```

```shell
ENV <key1>=<value1> <key2>=<value2> ...
```

#### （6）⽤于指定默认的容器主进程，每个Dockerfile中只能有⼀条CMD指令，如果有多条，则只有最后⼀条会⽣效；

```shell
CMD <命令>
```

```shell
CMD ["可执⾏⽂件", "参数1", "参数2" ...]
```

#### （7）⽤于定义匿名卷（持久化⽬录）

```shell
VOLUME <路径>
```

```shell
VOLUME ["路径1", "路径2"...]
```

#### （8）声明镜像创建者

```shell
MAINTAINER user_name
```

## Docker 开放2375端口


开启端口命令  （--permanent永久生效，没有此参数重启后失效）

```shell
firewall-cmd --zone=public --add-port=2375/tcp --permanent
```

重新载入

```shell
firewall-cmd --reload
```

使用 vim（vi） 编辑docker服务配置文件

```shell
vim /lib/systemd/system/docker.service
```

找到如下配置行

```shell
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```

将其注释掉或者直接删除，替换成下面的配置行.然后保存退出

```shell
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
```

重新加载docker配置并重启服务

```shell
systemctl daemon-reload && systemctl restart docker
```

然后直接在命令行客户端输入如下命令，IP地址改为自己的

```shell
curl http://IP地址:2375/version
```

或者在浏览器直接访问，IP地址改为自己的

```shell
http://IP地址:2375/version
```