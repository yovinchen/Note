# HomeBrew 安装 Node.js

简单的说 Node.js 就是运行在服务端的 JavaScript。Node.js 是一个基于 Chrome JavaScript 运行时建立的一个平台。Node.js 是一个事件驱动 I/O 服务端 JavaScript 环境，基于 Google 的 V8 引擎，V8 引擎执行 Javascript 的速度非常快，性能非常好。

此处使用 homebrew 安装，先确保安装Homebrew后再进行后续操作

先在终端输入下面命令查看是否安装HomeBrew 

```
brew -v
```

未安装可以看这篇文章安装 [Mac OS 神器 HomeBrew](https://blog.hhdxw.top/archives/242) 

### 搜索可安装的Node

```shell
brew search node
```

### 安装Node

安装默认版本

```shell
brew install node
```

安装指定版本

```shell l
brew install node@18
```

### 卸载Node

```shell
brew unistall node
```

### 查看Node版本

```shell
node -v
```

### 切换Node版本

切断16版本链接

```shell
brew unlink node@16
```

链接18版本

```shell
brew link --overwrite --force node@18
```

其中 Homebrew 包管理器中，`--overwrite` 和 `--force` 是两个选项，它们的作用如下：

1. `--overwrite` 选项

当 Homebrew 安装一个包时，如果这个包已经存在于系统中，Homebrew 默认会跳过这个包的安装。但是，如果您希望强制安装一个已经存在的包，可以使用 `--overwrite` 选项。

使用 `--overwrite` 选项可能会覆盖您系统中已经存在的一些文件，因此需要小心使用。请确保在使用这个选项前，已经备份了您的系统数据。

1. `--force` 选项

`--force` 选项是针对在 Homebrew 中进行软件包安装或卸载时出现错误的情况。有时，当您尝试安装或卸载一个软件包时，Homebrew 会输出一些警告或错误，阻止您完成操作。如果您确定这个操作是安全的，可以使用 `--force` 选项强制执行这个操作。

和 `--overwrite` 选项一样，`--force` 选项可能会导致系统中的文件被覆盖或者删除，因此也需要小心使用。它可能会破坏系统的稳定性，因此必须谨慎使用。

总之，`--overwrite` 和 `--force` 选项都是使 Homebrew 绕过某些限制和警告的选项，并且需要小心使用。在进行任何系统操作之前，请务必了解它们的意义和作用，并且备份您的系统数据。

### 卸载命令

```text
brew uninstall node
```

