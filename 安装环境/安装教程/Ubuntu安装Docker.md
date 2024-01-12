先更新源，并且安装必要的依赖软件

```shell
sudo apt update
sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

导入源仓库的 GPG key

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

添加 Docker APT 软件源

```shell
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

安装 Docker 最新版本

```shell
sudo apt install docker-ce docker-ce-cli containerd.io
```

至此安装完成。  
输入命令验证是否安装成功，查看是否显示Docker版本号。

```shell
docker version
```

```shell
sudo apt-get update
sudo apt install docker-compose-plugin
```