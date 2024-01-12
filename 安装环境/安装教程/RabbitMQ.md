# RabbitMQ

### 1.查看仓库

```shell
docker search rabbitmq
```

### 2.安装RabbitMQ

```shell
docker pull rabbitmq
```

### 3.启动RabbitMQ

```shell
docker run -d --hostname my-rabbit --name rabbit -p 15672:15672 -p 5672:5672 rabbitmq
```

```shell
docker run -d --restart=always -p 5672:5672 -p 15672:15672 --name rabbitmq rabbitmq:3.8-management
```
### 4.安装插件

找到镜像ID

```shell
docker ps
```

进入容器

```shell
docker exec -it my-rabbit /bin/bash
```

安装插件

```shell
rabbitmq-plugins enable rabbitmq_management
```

ctrl+p+q退出当前容器

访问地址

http://服务器IP:15672，用户名密码都是`guest`

### 5.踩坑

进入rabbitmq容器
```shell
docker exec -it {rabbitmq容器名称或者id} /bin/bash
```

进入容器后，cd到以下路径

```shell
cd /etc/rabbitmq/conf.d/
```

修改 management_agent.disable_metrics_collector = false

```shell
echo management_agent.disable_metrics_collector = false > management_agent.disable_metrics_collector.conf
```

退出容器

```shell
exit
```

重启rabbitmq容器

```shell
docker restart {rabbitmq容器id}
```

### 6.rabbit 创建用户授权

添加用户，用户名为root,密码为123456

```shell
rabbitmqctl add_user admin admin
```

赋予root用户所有权限

```shell
rabbitmqctl set_permissions -p / root ".*" ".*" ".*"
```

赋予root用户administrator角色

```shell
rabbitmqctl set_user_tags root administrator
```

查看所有用户

```shell
rabbitmqctl list_users
```