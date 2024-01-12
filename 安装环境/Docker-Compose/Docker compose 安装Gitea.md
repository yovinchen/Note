首先检查docker-compose的安装情况

```shell
docker-compose -v
```

创建Docker-Compose

```shell
cd /var/lib
mkdir docker-compose
```

编辑docker-compose文件

```shell
vi docker-compose.yml
```

在文件夹下编写`docker-compose.yml`文件

```yml
version: "3"

networks:
  gitea:
    external: false

services:
  server:
    image: gitea/gitea:1.20.5
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
    restart: always
    networks:
      - gitea
    volumes:
      - ./gitea:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "10800:3000"
      - "2222:22"
```

启动docker-compose

```shell
docker-compose up -d
```

检查运行情况

```shell
docker ps
docker-compose ps
```

