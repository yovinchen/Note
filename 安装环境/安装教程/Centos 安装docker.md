​              

更新一下环境：

```
yum -y update
```

安装一些必要的系统工具：

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

添加软件源信息：

```
sudo yum install -y yum-utils
```

国内阿里源

```
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 
```

docker官方镜像源

```
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo 
```

安装 Docker：

```
sudo yum install docker-ce docker-ce-cli containerd.io
```

启动 Docker 后台服务

```
sudo systemctl start docker
sudo systemctl enable docker
```

测试运行 hello-world

```
docker run hello-world
```

部分镜像源

```shell
sudo vim /etc/docker/daemon.json
```

镜像源

```
{
  "registry-mirrors": [
    "https://9kkc1zdn.mirror.aliyuncs.com",
    "https://hub-mirror.c.163.com/",
    "https://docker.mirrors.ustc.edu.cn/",
    "https://registry.docker-cn.com"
    "https://hub.docker.com"
  ]
}
```

上传地址设置

```json

```

重启开启镜像源

```shell
systemctl daemon-reload

systemctl restart docker
```

安装 Docker-compose


```shell
yum install docker-compose-plugin
```

开放所有端口

```shell
firewall-cmd --zone=public --add-port=1-65535/udp --permanent
firewall-cmd --zone=public --add-port=1-65535/tcp --permanent
firewall-cmd --reload
```
