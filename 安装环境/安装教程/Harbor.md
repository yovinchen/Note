Harbor是一个开源的可信云原生注册表，用于存储、签名和扫描内容。它为开源Docker发行版添加了安全、身份和管理等功能。

## 自动安装

```console
curl -LO https://raw.githubusercontent.com/bitnami/containers/main/bitnami/harbor-portal/docker-compose.yml

curl -L https://github.com/bitnami/containers/archive/main.tar.gz | tar xz --strip=2 containers-main/bitnami/harbor-portal && cp -RL harbor-portal/config . && rm -rf harbor-portal

docker-compose up
```

## 手动安装

### 1、下载安装包

```shell
wget https://github.com/goharbor/harbor/releases/download/v2.7.4/harbor-offline-installer-v2.7.4.tgz
```

### 2、解压安装包,进入目录并展示文件

```shell
tar -xvf harbor-offline-installer-v2.7.4.tar
cd harbor
ll
```

![image-20231208150823727](https://lsky.hhdxw.top/imghub/2023/12/image-202312081702019310.png)

### 3、复制harbor.yml配置文件并编辑

```shell
cp harbor.yml.tmpl harbor.yml
vim harbor.yml
```

### 4、编辑文件内容如图

![image-20231208151131781](https://lsky.hhdxw.top/imghub/2023/12/image-202312081702019492.png)

### 5、启动项目

```shell
./install.sh
```

即可看到安装，等待安装完毕即可。

## 简单使用

首先进入页面，输入上面设置的密码登录（默认访问80端口，ip:80）

![image-20231208151408705](https://lsky.hhdxw.top/imghub/2023/12/image-202312081702019649.png)

进入页面后新建一个项目例如 `public`

![image-20231208151853837](https://lsky.hhdxw.top/imghub/2023/12/image-202312081702019934.png)

访问级别是公开，存储限制为无限（镜像代理可以去配置aliyun，此代理是用户在harbor仓库中找不到，对应镜像，然后去代理仓库中查找镜像）

![image-20231208152259575](https://lsky.hhdxw.top/imghub/2023/12/image-202312081702020179.png)

打开另外一台服务器，配置`docker`镜像设置



```shell
sudo vim /etc/docker/daemon.json
```

将下面的内容复制进去（`<ip>`更换为上述`Harbor`服务器地址）

```xml
{
  "registry-mirrors": [
		"https://hub.docker.com",
    "http://<ip>:80"
  ],
  "insecure-registries" : [
    "<ip>:80"
  ]
}
```

保存退出，然后重新加载配置启动`docker`

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```

在本地镜像（下载了一个`redis`作为演示）打一个标签

```shell
#下载镜像
docker pull redis

#给镜像打标签
# redis:latest 被打标签的本地镜像
# <ip>:80/public/redis:v1新的标签名称
# <ip> 是指定的 IP 地址，80 是端口号，public/redis:v1 是新的标签。
docker tag redis:latest <ip>:80/public/redis:v1


#登录远程Harbor仓库
docker login -u <username> -p <password> http://<ip>:80

#推送镜像
docker push <ip>:80/public/redis:v1
```

![image-20231208154658318](https://lsky.hhdxw.top/imghub/2023/12/image-202312081702021618.png)

在`Harbor`中就可以看到镜像了

![image-20231208230150057](https://lsky.hhdxw.top/imghub/2023/12/image-202312081702047710.png)