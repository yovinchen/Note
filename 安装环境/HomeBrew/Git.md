# HomeBrew 安装 Git

Git 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。Git 是 Linus Torvalds 为了帮助管理 Linux 内核开发而开发的一个开放源码的版本控制软件。Git 与常用的版本控制工具 CVS, Subversion 等不同，它采用了分布式版本库的方式，不必服务器端软件支持。

此处使用 homebrew 安装，先确保安装Homebrew后再进行后续操作

先在终端输入下面命令查看是否安装HomeBrew 

```
brew -v
```

未安装可以看这篇文章安装 [Mac OS 神器 HomeBrew](https://blog.hhdxw.top/archives/242) 

### 查看搜索可安装

```shell
brew install git
```

### 安装Git

```shell
brew install git
```

### 查看Git版本

```shell
git --version
```

### 配置 SSH key

### 输入如下命令产生新的key

```shell
ssh-keygen -t rsa -C "your_email@example.com"
```

### 将SSH key添加到Github

登录到Github页面 -> 右上角Setttings -> SSH keys ->Add key

查看生成的key内容：

```shell
cat ~/.ssh/id_rsa.pub
```

将以上内容复制到 Github ==> Setting ==>Key 中完成添加新的key。

## 配置Git用户信息

```shell
git config --global user.name   "你的名字或昵称"
git config --global user.email  "你的邮箱"
```

### 卸载命令

```text
brew uninstall git
```

