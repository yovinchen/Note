

开启端口命令  （--permanent永久生效，没有此参数重启后失效）

```
firewall-cmd --zone=public --add-port=2375/tcp --permanent
```

重新载入

```
firewall-cmd --reload
```

使用 vim 编辑docker服务配置文件

```
vim /lib/systemd/system/docker.service
```

找到如下配置行

```
ExecStart=/usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```

将其注释掉或者直接删除，替换成下面的配置行.然后保存退出

```
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock
```

重新加载docker配置并重启服务

```
systemctl daemon-reload && systemctl restart docker
```

然后直接在命令行客户端输入如下命令，IP地址改为自己的


```
curl http://IP地址:2375/version
```

或者在浏览器直接访问，IP地址改为自己的

```
http://IP地址:2375/version
```


```xml
{
    "insecure-registries": [
    "10.211.55.2:8082"
  ],
  "registry-mirrors": [
    "10.211.55.2:8082"
  ]
}
```