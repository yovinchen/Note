# smartctl 硬盘读写查看

`smartctl` 是一款基于命令行的磁盘自我监测分析报告技术（Self-Monitoring, Analysis and Reporting Technology，简称 SMART）工具，用于对硬盘进行读写测试、状态检查以及管理。它能够获取硬盘的各种指标信息，如温度、错误率、剩余寿命等，并通过判断这些指标信息来评估硬盘的健康状态。

此处使用 homebrew 安装，先确保安装Homebrew后再进行后续操作

先在终端输入下面命令查看是否安装HomeBrew 

```
brew -v
```

未安装可以看这篇文章安装 [Mac OS 神器 HomeBrew](https://blog.hhdxw.top/archives/242) 

在 macOS 系统中，可以通过 Homebrew 软件包管理器安装 `smartctl` 命令，步骤如下：

### 更新 Homebrew 软件包管理器

```shell
brew update
```

### 搜索可安装

```
brew search smartmontools
```

### 安装 smartmontools

```shell
brew install smartmontools
```

### 显示硬盘的 SMART 信息

```shell
smartctl -a /dev/disk0
```

其中，`-a` 选项用于显示所有可用的 SMART 信息，`/dev/disk0` 表示要检测的硬盘设备文件。

![c87f114b790cfba15db88f5e1d1d9e96](https://yovinchen-1308133012.cos.ap-beijing.myqcloud.com/c87f114b790cfba15db88f5e1d1d9e96.png)

结果如下，里面的Percentage Used 就是损耗值，Data Units Written 就是写入量。

其中这么大的写入读取量全部是由于 mac os 相对激进的 swap 策略

### 卸载命令

```text
brew uninstall smartmontools 
```

