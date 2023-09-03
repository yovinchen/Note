## Docker 2375端口开启外网访问

进入服务器后

```shell
sudo vim /etc/systemd/system/multi-user.target.wants/docker.service
```

添加配置，i进行插入

```shell
-H tcp://0.0.0.0
```

![image-20230901170301087](https://lsky.hhdxw.top/imghub/2023/09/iamge-202309011693558981.png)

esc :wq保存退出

然后重启docker服务

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker.service	
```

