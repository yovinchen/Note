# Mac OS 开发神器 HomeBrew 

Homebrew 是一款适用于 macOS 系统的自由及开放源代码软件包管理系统，旨在使软件安装更加简单。Homebrew 提供了一个方便的命令行界面，允许用户通过简单的命令来安装、更新和卸载许多常用的开源软件包，如 Git、Python、Node.js 等等。Homebrew 可以帮助你轻松地维护你的软件环境，并且可以让你在使用 macOS 的同时也能够享受到 Linux 的软件包管理方式。使用 Homebrew，你可以避免手动编译和安装软件包，节省你的时间和精力。

首先是HomeBrew的官网 https://brew.sh/index_zh-cn

那么我们就先来安装homebrew

## Homebrew 安装命令

在 Mac 上安装 Homebrew： 

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

在 Linux 上安装 Homebrew：

```shell
 /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

在 Windows 上安装 Homebrew：

```shell
N/A(homebrew 暂时不支持windows)
```

上面的都是国外的镜像源没有科技的小伙伴们的速度都会比较慢，下面是一些国内镜像源的安装地址

中科大镜像源：

```shell
/bin/bash -c "$(curl -fsSL https://mirrors.ustc.edu.cn/brew-install/install.sh)"
```

清华镜像源：

```shell
/bin/bash -c "$(curl -fsSL https://mirrors.tuna.tsinghua.edu.cn/homebrew/install/master/install.sh)"
```

阿里云镜像源：

```shell
/bin/bash -c "$(curl -fsSL https://mirrors.aliyun.com/homebrew/install/install.sh)"
```

## Homebrew 常用命令

其中`<package_name>`为需要安装的包名，例如：node 

### 1、搜索软件包：

这个命令用于查找 Homebrew 中包含特定关键字的软件包。可以在 `<keyword>` 参数中输入任何与要查找的软件包相关的词汇或名称，Homebrew 会返回所有包含该词的软件包列表

```shell
brew search <keyword>
```

### 2、安装软件包：

这个命令可以安装指定的软件包

```shell
brew install <package_name>
```

### 3、卸载软件包：

这个命令可以卸载指定的软件包

```shell
brew uninstall <package_name>
```

### 4、更新 Homebrew：

这个命令可以更新 Homebrew 自身，包括 Homebrew 的程序代码、依赖项和其他组件

```shell
brew update
```

### 5、查看哪些软件需要更新：

这个命令用于显示已安装软件包中哪些需要更新

```shell
brew outdated
```

### 6、升级已安装的软件包：

这个命令用于升级所有已安装的软件包。

```shell
brew upgrade
```

### 7、查看已安装的软件包：

这个命令可以列出已安装的所有软件包。

```shell
brew list
```

### 8、更新指定的包

使用这个命令可以只升级指定的软件包。

```
brew upgrade <package_name>
```

### 9、清理所有包的旧版本

这个命令可以清理所有软件包的旧版本

```
brew cleanup
```

### 10、显示软件包信息：

这个命令可以显示有关指定软件包的详细信息，包括版本、依赖项和安装路径等

```shell
brew info <package_name>
```

### 11、切换指定包版本：

这个命令用于切换已经安装的软件包到指定版本

这个命令查看是否存在想要切换的版本

```shell
brew search <package_name>
```

这个命令切断当前版本的链接

```shell
brew unlink <package_name>@<version>
```

这个命令连接新版本的链接

```shell
brew link <package_name>@<version>
```

### 12、显示已安装软件包的依赖关系

这个命令可以查看已安装软件包的依赖关系

```shell
brew deps <package_name>
```

### 13、显示软件包安装目录

这个命令可以查看软件包的安装目录

```shell
brew --prefix <package_name>
```

### 14、显示软件包的版本号

这个命令可以查看软件包的版本号、依赖关系和安装路径等信息

```shell
brew info <package_name>
```

### 15、更改国内镜像源

由于某些网络限制或其他原因，Homebrew 在国内可能会受到网络访问不畅的影响。可以通过更改国内镜像源来解决这个问题。例如，要将 Homebrew 镜像源更改为中科大镜像源，可以使用以下命令：

```shell
/bin/bash -c "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/install/install.sh)"
```

### 16、查看 Homebrew 版本号

这个命令查看当前安装的 Homebrew 版本号。

```shell
brew --version
```

