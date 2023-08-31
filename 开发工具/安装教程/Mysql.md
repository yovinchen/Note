# Docker 安装 Mysql

首先安装`Dcoker`没有安装可以参考这篇文章[Docker基本命令](https://blog.hhdxw.top/archives/235)

### 1、查看可用的 Mysql 版本

访间问 Mysql 镜像库地址：https://hub.docker.com

也可以用命令来查看

```bash
docker search mysql
```

### 2、拉取指定版本的 Mysql 镜像

这里用最新版

```bash
docker pull mysql:latest

docker pull mysql:8
```

### 3、查看本地镜像

用以下命令查看是否安装了Mysql

```bash
docker images
```

### 4、运行容器

```bash
docker run --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -d mysql:latest
```

- `docker run`：启动一个新的容器。
- `--name mysql`：为容器命名为 `mysql`，以便在进一步操作时可以直接使用这个名称而不是 ID。
- `-p 3306:3306`：将主机的 3306 端口映射到容器内的 3306 端口。
- `-e MYSQL_ROOT_PASSWORD=root`：设置 MySQL 的 root 用户的密码为 `root`。这个设置会被传递给容器内部，并通过环境变量 `MYSQL_ROOT_PASSWORD` 进行读取。
- `-d mysql:latest`：使用 MySQL 的最新版本镜像，并在后台运行容器。

### 5、安装成功

最后我们可以通过查看运行项

```bash
docker ps 
```

命令查看容器的运行信息

### 6、开启远程访问

进入容器

```bash
docker exec -it 容器名称|容器id bin/bash
```

登录mysql，打开远程访问

1.登录mysql

```bash
mysql -uroot -p
```
2.进行授权

```bash
GRANT ALL ON *.* TO 'root'@'%';\
```
3.刷新权限

```bash
flush privileges;
```
4.加密规则

```bash
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;
```
5.更新root密码

```bash
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';
```
6.刷新权限

```bash
flush privileges;
```



### 6、测试

在 docker 可视化工具里可以直接访问 msyql

例如：[Docker Desktop](https://www.docker.com/products/docker-desktop/)、Docker中的[portainer](https://www.portainer.io/)

或者使用数据库管理工具访问mysql

例如：[Navicat](https://www.navicat.com.cn/)、[WorkBench](https://dev.mysql.com/downloads/workbench/)

### 7、停止容器

```bash
docker stop mysql
```

### 8、关闭容器

```bash
docker rm mysql
```

