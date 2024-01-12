# 本地部署K8s集群(一键部署)

[TOC]

## 准备 Linux 主机

### 系统要求

| **Ubuntu** *16.04，18.04，20.04*                             | CPU：2 核，内存：4 G，硬盘：40 G |
| ------------------------------------------------------------ | -------------------------------- |
| **Debian** *Buster，Stretch*                                 | CPU：2 核，内存：4 G，硬盘：40 G |
| **CentOS** *7*.x                                             | CPU：2 核，内存：4 G，硬盘：40 G |
| **Red Hat Enterprise Linux** *7*                             | CPU：2 核，内存：4 G，硬盘：40 G |
| **SUSE Linux Enterprise Server** *15* **/openSUSE Leap** *15.2* | CPU：2 核，内存：4 G，硬盘：40 G |

- CPU 必须为 x86_64，暂时不支持 Arm 架构的 CPU。
- 系统应该为纯净镜像不包含其他服务

### 节点要求

- 所有节点必须都能通过 `SSH` 访问。
- 所有节点时间同步。
- 所有节点都应使用 `sudo`/`curl`/`openssl`/`tar`。

### 容器要求（可以不安装容器）

| 支持的容器运行时              | 版本    |
| :---------------------------- | :------ |
| Docker                        | 19.3.8+ |
| containerd                    | 最新版  |
| CRI-O（试验版，未经充分测试） | 最新版  |
| iSula（试验版，未经充分测试） | 最新版  |

### 依赖项要求

| 依赖项      | Kubernetes 版本 ≥ 1.18 | Kubernetes 版本 < 1.18 |
| :---------- | :--------------------- | :--------------------- |
| `socat`     | 必须                   | 可选，但建议安装       |
| `conntrack` | 必须                   | 可选，但建议安装       |
| `ebtables`  | 可选，但建议安装       | 可选，但建议安装       |
| `ipset`     | 可选，但建议安装       | 可选，但建议安装       |

### 网络和 DNS 要求

- 请确保 `/etc/resolv.conf` 中的 DNS 地址可用，否则，可能会导致集群中的 DNS 出现问题。
- 网络配置使用防火墙规则或安全组，请务必确保基础设施组件可以通过特定端口相互通信。建议您关闭防火墙。

### 演示机器准备

| 主机 IP         | 主机名 | 角色                | 配置                             |
| :-------------- | :----- | :------------------ | -------------------------------- |
| 192.168.188.138 | master | control plane, etcd | CPU：4 核，内存：8 G，硬盘：40 G |
| 192.168.188.143 | node1  | worker              | CPU：4 核，内存：8 G，硬盘：40 G |
| 192.168.188.144 | node2  | worker              | CPU：4 核，内存：8 G，硬盘：40 G |

## 初始化系统环境

所有服务器均需执行

### 更新yum源

```shell
yum -y update
```

### 配置主机名

```shell
#查看主机名称
hostname
#更改名称 hostnamectl --static set-hostname 服务器名称
hostnamectl --static set-hostname master
#查看主机名称核验
hostnamectl status
#可以重启一下服务器
#reboot
```

### 关闭防火墙

```shell
systemctl disable firewalld
systemctl stop firewalld
```

### 打开端口转发

```shell
firewall-cmd --add-masquerade --permanent # 端口转发
firewall-cmd --reload
firewall-cmd --list-all
firewall-cmd --list-all --zone=trusted
```

### 开放全部端口

```shell
firewall-cmd --zone=public --add-port=1-65535/udp --permanent
firewall-cmd --zone=public --add-port=1-65535/tcp --permanent
firewall-cmd --reload
```

### 禁用 SELinux

```shell
# 使用 sed 修改配置文件，实现彻底的禁用
sed -i 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config

# 使用命令，实现临时禁用，这一步其实不做也行，KubeKey 会自动配置
setenforce 0
```

### 关闭swap分区

```shell
swapoff -a
echo "vm.swappiness=0" >> /etc/sysctl.conf
sysctl -p /etc/sysctl.conf
```

### 配置epel源（国内服务器）

```
rpm -ivh http://mirrors.aliyun.com/epel/epel-release-latest-7.noarch.rpm
```

### 配置服务器时区

```shell
#配置服务器时区为 Asia/Shanghai
timedatectl set-timezone Asia/Shanghai
#验证
timedatectl
```

### 配置时间同步

```shell
#安装 chrony 作为时间同步软件
yum install chrony -y
```

```shell
#修改配置文件 /etc/chrony.conf，修改 ntp 服务器配置。
vi /etc/chrony.conf

# 删除所有的 server 配置
# server 0.centos.pool.ntp.org iburst
# server 1.centos.pool.ntp.org iburst
# server 2.centos.pool.ntp.org iburst
# server 3.centos.pool.ntp.org iburst

# 增加国内的 ntp 服务器，或是指定其他常用的时间服务器
pool cn.pool.ntp.org iburst
```

```shell
#重启并设置 chrony 服务开机自启动。
systemctl enable chronyd --now
```

```shell
#验证 chrony 同步状态。
chronyc sourcestats -v
```

```shell
# 正常的输出结果如下
[root@master ~]# chronyc sourcestats -v
210 Number of sources = 4
                             .- Number of sample points in measurement set.
                            /    .- Number of residual runs with same sign.
                           |    /    .- Length of measurement set (time).
                           |   |    /      .- Est. clock freq error (ppm).
                           |   |   |      /           .- Est. error in freq.
                           |   |   |     |           /         .- Est. offset.
                           |   |   |     |          |          |   On the -.
                           |   |   |     |          |          |   samples. \
                           |   |   |     |          |          |             |
Name/IP Address            NP  NR  Span  Frequency  Freq Skew  Offset  Std Dev
==============================================================================
tock.ntp.infomaniak.ch     41  22  297m     -0.227      0.504    -12ms  4011us
makaki.miuku.net           26  10  312m     -0.014      0.497    +27ms  4083us
electrode.felixc.at        22  15  241m     -0.399      1.350    -49ms  6966us
ntp5.flashdance.cx         38  14  315m     +0.363      0.600    +27ms  4865us
关闭系统防火墙
```

### 安装依赖组件

```shell
yum install -y ebtables socat ipset conntrack
```

### 配置基于 SSH 密钥的身份验证(可选)

仅需在`master` 主节点运行

```shell
#使用 ssh-keygen 命令生成一个新的 SSH 密钥对,一直按回车直到生密钥对
ssh-keygen -t ed25519
```

```shell
#将 SSH 公钥从 master 节点发送到其他节点。命令执行时输入 yes，以接受服务器的 SSH 指纹，然后在出现提示时输入 root 用户的密码。
ssh-copy-id root@192.168.188.143
ssh-copy-id root@192.168.188.144
```

```shell
#验证登录
ssh root@192.168.188.143
ssh root@192.168.188.144
```

```shell
#输出结果如下
[root@master ~]# ssh root@192.168.188.143
Last login: Tue Oct 24 00:29:36 2023 from 192.168.188.138
[root@node1 ~]# 
```

### 升级 CentOS7 内核(可选)

仅需主`master` 主节点运行

CentOS 7.9 的默认内核版本为 **3.10.0-1160.102.1**，与其他操作系统内核版本动辄 5.x、6.x 相比

### 查看当前系统内核版本

```shell
uname -r

# 3.10.0-1160.102.1.el7.x86_64
```

### 查询当前系统与 Kernel 相关的软件包

```shell
#查询当前系统安装了哪些跟 Kernel 有关的软件包，升级内核的时候，一定要把已安装的相关 kernel 包一起升级。 
rpm -qa | grep kernel

#kernel-3.10.0-1160.102.1.el7.x86_64
#kernel-tools-3.10.0-1160.102.1.el7.x86_64
#kernel-tools-libs-3.10.0-1160.102.1.el7.x86_64
```

### 增加 ELRepo 软件源

```shell
#导入 RPM GPG public key
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org

#安装 ELRepo
yum install https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm
```

### 查询可用的内核软件包

```shell
#启用新增加的 ELRepo 软件仓库，查询可用的内核软件包。
yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
```

```shell
[root@master ~]# yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * elrepo-kernel: mirrors.tuna.tsinghua.edu.cn
可安装的软件包
kernel-lt-devel.x86_64                      5.4.258-1.el7.elrepo            elrepo-kernelkernel-lt-doc.noarch                        5.4.258-1.el7.elrepo            elrepo-kernelkernel-lt-headers.x86_64                    5.4.258-1.el7.elrepo            elrepo-kernelkernel-lt-tools-libs-devel.x86_64           5.4.258-1.el7.elrepo            elrepo-kernelkernel-ml.x86_64                            6.5.8-1.el7.elrepo              elrepo-kernelkernel-ml-devel.x86_64                      6.5.8-1.el7.elrepo              elrepo-kernelkernel-ml-doc.noarch                        6.5.8-1.el7.elrepo              elrepo-kernelkernel-ml-headers.x86_64                    6.5.8-1.el7.elrepo              elrepo-kernelkernel-ml-tools.x86_64                      6.5.8-1.el7.elrepo              elrepo-kernelkernel-ml-tools-libs.x86_64                 6.5.8-1.el7.elrepo              elrepo-kernelkernel-ml-tools-libs-devel.x86_64           6.5.8-1.el7.elrepo              elrepo-kernelperf.x86_64                                 5.4.258-1.el7.elrepo            elrepo-kernelpython-perf.x86_64                          5.4.258-1.el7.elrepo            elrepo-kernel[root@master ~]# 
#当前最新 lt 版内核为 5.4.258-1 a信用最新版本内核
```

### 安装新版本内核

```shell
#只安装内核，同时安装其他包会报错
yum --enablerepo=elrepo-kernel install kernel-lt

#安装成功会提示
#Complete!
```



### 配置新内核引导系统

```shell
#查看已经安装的 kernel 信息
grubby --info=ALL | grep ^kernel

#kernel=/boot/vmlinuz-5.4.258-1.el7.elrepo.x86_64
#kernel=/boot/vmlinuz-3.10.0-1160.102.1.el7.x86_64
#kernel=/boot/vmlinuz-3.10.0-1062.el7.x86_64
#kernel=/boot/vmlinuz-0-rescue-fc61eb831f5b4eda852bfd7c596c8c87
```

```shell
#查看当前系统默认内核
grubby --default-kernel

#/boot/vmlinuz-3.10.0-1160.102.1.el7.x86_64
```

```shell
#修改系统默认内核为新内核
grubby --set-default "/boot/vmlinuz-5.4.258-1.el7.elrepo.x86_64"
```

```shell
#查看系统默认内核
grubby --default-kernel
/boot/vmlinuz-5.4.258-1.el7.elrepo.x86_64
```

```shell
#重启
reboot
```

进入引导画面后选中最新的内核默认启动

![image-20231024093226585](https://lsky.hhdxw.top/imghub/2023/10/image-202310241698111147.png)

### 重启系统并查看系统内核

```shell
#查看内核版本信息
uname -rs
#Linux 5.4.258-1.el7.elrepo.x86_64
```

```shell
#查看已经安装的内核相关软件
rpm -qa | grep kernel
#kernel-lt-5.4.258-1.el7.elrepo.x86_64
#kernel-lt-tools-3.10.0-1160.102.1.el7.elrepo.x86_64
#kernel-3.10.0-1160.102.1.el7.x86_64
#kernel-3.10.0-1062.el7.x86_64
#kernel-lt-tools-libs-3.10.0-1160.102.1.el7.elrepo.x86_64
```

```shell
# 卸载旧版本的 kernel-tools 相关软件包
yum remove kernel-tools-3.10.0-1160.102.1.el7.x86_64 kernel-tools-libs-3.10.0-1160.102.1.el7.x86_64

# 安装新版本的 kernel-tools 相关软件包
yum --enablerepo=elrepo-kernel install kernel-lt-tools kernel-lt-tools-libs
```

## 下载 KubeKey

下面只需要 `master` 主节点运行即可

先执行以下命令以确保您从正确的区域下载 KubeKey。

```shell
export KKZONE=cn
```

执行以下命令下载 KubeKey：

```shell
curl -sfL https://get-kk.kubesphere.io | VERSION=v3.0.7 sh -
```

为 `kk` 添加可执行权限：

```shell
chmod +x kk
```

## 创建集群

### 查看 KubeKey 支持的 Kubernetes 版本列表

```shell
./kk version --show-supported-k8s
```

### 创建配置文件

命令如下：

```shell
./kk create config [--with-kubernetes version] [--with-kubesphere version] [(-f | --file) path]
```

- 安装 KubeSphere 3.3 的建议 Kubernetes 版本：v1.20.x、v1.21.x、* v1.22.x、* v1.23.x 和 * v1.24.x。带星号的版本可能出现边缘节点部分功能不可用的情况。因此，如需使用边缘节点，推荐安装 v1.21.x。如果不指定 Kubernetes 版本，KubeKey 将默认安装 Kubernetes v1.23.10。
- 如果您在此步骤的命令中不添加标志 `--with-kubesphere`，则不会部署 KubeSphere，只能使用配置文件中的 `addons` 字段安装，或者在您后续使用 `./kk create cluster` 命令时再次添加这个标志。
- 如果您添加标志 `--with-kubesphere` 时不指定 KubeSphere 版本，则会安装最新版本的 KubeSphere。

示例：（默认K8s版本1.23.10，KubeSphere3.3.2）

```shell
./kk create config --with-kubesphere 3.3.2
```

### 编辑配置文件

如果不改名称，那么将创建默认文件 `config-sample.yaml`。

```shell
vi config-sample.yaml
```

### 主机

请参照上方示例在 `hosts` 下列出您的所有机器并添加详细信息。

`name`：实例的主机名。

`address`：任务机和其他实例通过 SSH 相互连接所使用的 IP 地址。根据您的环境，可以是公有 IP 地址或私有 IP 地址。例如，一些云平台为每个实例提供一个公有 IP 地址，用于通过 SSH 访问。在这种情况下，您可以在该字段填入这个公有 IP 地址。

`internalAddress`：实例的私有 IP 地址。

### roleGroups

- `etcd`：etcd 节点名称
- `control-plane`：主节点名称
- `worker`：工作节点名称

### 提示

- 在安装 KubeSphere 之前，您可以使用 `hosts` 下提供的信息（例如 IP 地址和密码）通过 SSH 的方式测试任务机和其他实例之间的网络连接。

- 在安装前，请确保端口 `6443` 没有被其他服务占用，否则在安装时会产生冲突（`6443` 为 API 服务器的默认端口）。

### 示例：

```yml

apiVersion: kubekey.kubesphere.io/v1alpha2
kind: Cluster
metadata:
  name: sample
spec:
  hosts:
  - {name: master, address: 192.168.188.138, internalAddress: 192.168.188.138, user: root, password: root}
 #上面配置ssh密钥对以后可以使用秘钥直接访问，如没有配置，请使用下面注释内容通过远程访问
  - {name: node1, address: 192.168.188.143, internalAddress: 192.168.188.143, privateKeyPath: "~/.ssh/id_ed25519"}
  - {name: node2, address: 192.168.188.144, internalAddress: 192.168.188.144, privateKeyPath: "~/.ssh/id_ed25519"}
 #- {name: node1, address: 192.168.188.143, internalAddress: 192.168.188.143, user: root, password: root}
 #- {name: node2, address: 192.168.188.144, internalAddress: 192.168.188.144, user: root, password: root}
  roleGroups:
    etcd:
    - master
    control-plane:
    - master
    worker:
    - node1
    - node2
```

### 使用配置文件创建集群

```shell
./kk create cluster -f config-sample.yaml
```

如果使用其他名称，则需要将上面的 `config-sample.yaml` 更改为您自己的文件。

整个安装过程可能需要 10 到 20 分钟，具体取决于您的计算机和网络环境。

### 验证安装

安装完成后，您会看到如下内容：

```shell
#####################################################
###              Welcome to KubeSphere!           ###
#####################################################

Console: http://192.168.188.138:30880
Account: admin
Password: P@88w0rd
NOTES：
  1. After you log into the console, please check the
     monitoring status of service components in
     "Cluster Management". If any service is not
     ready, please wait patiently until all components 
     are up and running.
  2. Please change the default password after login.

#####################################################
https://kubesphere.io             2023-10-24 00:37:49
#####################################################
```

## 安装插件

### devops

以 `admin` 用户登录控制台，点击左上角的**平台管理**，选择**集群管理**。

点击**定制资源定义**，在搜索栏中输入 `clusterconfiguration`，点击搜索结果查看其详细页面。

定制资源定义（CRD）允许用户在不新增 API 服务器的情况下创建一种新的资源类型，用户可以像使用其他 Kubernetes 原生对象一样使用这些定制资源。

在**自定义资源**中，点击 `ks-installer` 右侧的更多，选择**编辑 YAML**。

在该 YAML 文件中，搜索 `devops`，将 `enabled` 的 `false` 改为 `true`。完成后，点击右下角的**确定**，保存配置。

```
devops:
  enabled: true # 将“false”更改为“true”。
```

添加`minio`版本,在spec下面添加下面四个地址

```shell
spec:
  minio_repo: docker.io.minio/minio
  minio_tag: RELEASE.2021-12-29T06-49-06Z
  mc_repo: docker.io.minio/mc
  mac_tag: RELEASE.2021-12-29T06-52-55Z
```

在 kubectl 中执行以下命令检查安装过程：

```
kubectl logs -n kubesphere-system $(kubectl get pod -n kubesphere-system -l 'app in (ks-install, ks-installer)' -o jsonpath='{.items[0].metadata.name}') -f
```

## 卸载插件

### devops

卸载 DevOps：

```
helm uninstall -n kubesphere-devops-system devops
kubectl patch -n kubesphere-system cc ks-installer --type=json -p='[{"op": "remove", "path": "/status/devops"}]'
kubectl patch -n kubesphere-system cc ks-installer --type=json -p='[{"op": "replace", "path": "/spec/devops/enabled", "value": false}]'
```

删除 DevOps 资源：

```
# 删除所有 DevOps 相关资源
for devops_crd in $(kubectl get crd -o=jsonpath='{range .items[*]}{.metadata.name}{"\n"}{end}' | grep "devops.kubesphere.io"); do
    for ns in $(kubectl get ns -ojsonpath='{.items..metadata.name}'); do
        for devops_res in $(kubectl get $devops_crd -n $ns -oname); do
            kubectl patch $devops_res -n $ns -p '{"metadata":{"finalizers":[]}}' --type=merge
        done
    done
done
# 删除所有 DevOps CRD
kubectl get crd -o=jsonpath='{range .items[*]}{.metadata.name}{"\n"}{end}' | grep "devops.kubesphere.io" | xargs -I crd_name kubectl delete crd crd_name
# 删除 DevOps 命名空间
kubectl delete namespace kubesphere-devops-system
```
