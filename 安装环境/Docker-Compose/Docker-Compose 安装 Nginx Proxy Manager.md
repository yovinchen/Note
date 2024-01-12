首先检查docker-compose的安装情况

```shell
docker-compose -v
```

在文件夹下编写`docker-compose.yml`文件

```yml
version: '3.8'
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    restart: unless-stopped
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
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

```
admin@example.com
changeme
```