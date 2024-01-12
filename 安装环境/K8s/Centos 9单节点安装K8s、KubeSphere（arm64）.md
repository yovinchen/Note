# Centos 9单节点安装kubernetes、KubeSphere（arm64）

[TOC]

首先说一下什么使用centos 7 ，因为我的mac PD虚拟机安装不上包括centos Stream 8，为此只能换到centos Stream 9 了，我的云服务上跑的都是Centos 7，暂时用不惯 Ubuntu 。KubeSphere最新版本中部分组件不支持arm64，在后面的启动可查包组件中会提到如何解决。

下面是我搭建版本以及环境版本

|            |                          |
| ---------- | ------------------------ |
| 操作系统   | Centos Stream 9          |
| 系统配置   | 4核16GB内存 64GB磁盘空间 |
| kubernetes | 1.22.12                  |
| kubesphere | 3.4.0                    |

## 环境需求配置

### 官方支持的操作系统及最低配置

| 操作系统                                               | 最低配置                            |
| :----------------------------------------------------- | :---------------------------------- |
| **Ubuntu** *16.04*, *18.04*, *20.04*, *22.04*          | 2 核 CPU，4 GB 内存，40 GB 磁盘空间 |
| **Debian** *Buster*, *Stretch*                         | 2 核 CPU，4 GB 内存，40 GB 磁盘空间 |
| **CentOS** *7.x*                                       | 2 核 CPU，4 GB 内存，40 GB 磁盘空间 |
| **Red Hat Enterprise Linux 7**                         | 2 核 CPU，4 GB 内存，40 GB 磁盘空间 |
| **SUSE Linux Enterprise Server 15/openSUSE Leap 15.2** | 2 核 CPU，4 GB 内存，40 GB 磁盘空间 |

### 以下是支持的容器版本

| 支持的容器                    | 版本     |
| :---------------------------- | :------- |
| Docker                        | 19.3.8 + |
| containerd                    | 最新版   |
| CRI-O（试验版，未经充分测试） | 最新版   |
| iSula（试验版，未经充分测试） | 最新版   |

### Kubernetes依赖

| 依赖项    | Kubernetes 版本 ≥ 1.18 | Kubernetes 版本 < 1.18 |
| :-------- | :--------------------- | :--------------------- |
| socat     | 必须                   | 可选但建议             |
| conntrack | 必须                   | 可选但建议             |
| ebtables  | 可选但建议             | 可选但建议             |
| ipset     | 可选但建议             | 可选但建议             |

## 配置软件依赖

### 设置服务器hostname名称

```shell
#查看主机名称
hostname
#更改名称
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
systemctl status firewalld
```

### 关闭swap分区

```shell
swapoff -a
echo "vm.swappiness=0" >> /etc/sysctl.conf
sysctl -p /etc/sysctl.conf
```

### 配置epel源（国内服务器Centos7）

```shell
rpm -ivh http://mirrors.aliyun.com/epel/epel-release-latest-7.noarch.rpm
```

### Centos Stream 9更换了dnf包管理器

> 首先，需要安装extra包，并确保已安装了centos-release-stream包：
>
> ```shell
> sudo dnf install -y centos-release-stream
> ```
>
> 然后，使用下面的命令从EPEL存储库安装软件包：
>
> ```shell
> sudo dnf install -y epel-release
> ```
>
> 相较于以前的CentOS 7和8版本，CentOS Stream 9使用dnf作为默认的包管理器，而不是yum。dnf包管理器相较于yum，提供了更好地性能，依赖解决和自动同步功能。
>
> 建议在安装任何新的软件包之前，确保系统已经更新至最新状态：
>
> ```shell
> sudo dnf update -y
> ```
>
> 注意：EPEL存储库中存在由社区提供和维护的大量软件包。将这些包添加到服务器可能会给您带来未知的安全风险。在使用来自EPEL的软件包之前，请确保您都确定其安全性。

### 更新yum

```shell
yum -y update
```

### 安装依赖组件

```shell
yum install -y ebtables socat ipset conntrack
```

### 下载 KubeKey

```shell
curl -sfL https://get-kk.kubesphere.io | VERSION=v3.0.10 sh -

#访问gtihub受限制
#export KKZONE=cn
#curl -sfL https://get-kk.kubesphere.io | VERSION=v3.0.10 sh -
```

### 添加kk权限

```shell
chmod +x kk
```

### 开始安装（最小化安装）

模版

```shell
./kk create cluster [--with-kubernetes version] [--with-kubesphere version]
```

示例：同时安装 Kubernetes 和 KubeSphere

```shell
./kk create cluster --with-kubernetes v1.22.12 --with-kubesphere v3.4.0
```

- 安装 KubeSphere 3.4 的建议 Kubernetes 版本：v1.20.x、v1.21.x、v1.22.x、v1.23.x、* v1.24.x、* v1.25.x 和 * v1.26.x。带星号的版本可能出现边缘节点部分功能不可用的情况。因此，如需使用边缘节点，推荐安装 v1.23.x。如果不指定 Kubernetes 版本，KubeKey 将默认安装 Kubernetes v1.23.10。有关受支持的 Kubernetes 版本的更多信息，请参见[支持矩阵](https://www.kubesphere.io/zh/docs/v3.4/installing-on-linux/introduction/kubekey/#支持矩阵)。
- 一般来说，对于 All-in-One 安装，您无需更改任何配置。
- 如果您在这一步的命令中不添加标志 `--with-kubesphere`，则不会部署 KubeSphere，KubeKey 将只安装 Kubernetes。如果您添加标志 `--with-kubesphere` 时不指定 KubeSphere 版本，则会安装最新版本的 KubeSphere。
- KubeKey 会默认安装 [OpenEBS](https://openebs.io/) 为开发和测试环境提供 LocalPV 以方便新用户。对于其他存储类型，请参见[持久化存储配置](https://www.kubesphere.io/zh/docs/v3.4/installing-on-linux/persistent-storage-configurations/understand-persistent-storage/)。

输入 `yes` 继续安装流程。

### 验证安装结果

```shell
kubectl logs -n kubesphere-system $(kubectl get pod -n kubesphere-system -l 'app in (ks-install, ks-installer)' -o jsonpath='{.items[0].metadata.name}') -f
```

输出信息会显示 Web 控制台的 IP 地址和端口号，默认的 NodePort 是 `30880`。现在，您可以使用默认的帐户和密码 (`admin/P@88w0rd`) 通过 `<NodeIP>:30880` 访问控制台。

输出结果

```shell
#####################################################
###              Welcome to KubeSphere!           ###
#####################################################

Console: http://10.211.55.30:30880
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
https://kubesphere.io             2023-10-26 16:18:18
#####################################################
```

检查 KubeSphere 相关组件的运行状况

```shell
kubectl get pod --all-namespaces
```

## 启用可插拔组件（可选）

用于默认的最小化安装。若要在 KubeSphere 中启用其他组件，请参见[启用可插拔组件](https://www.kubesphere.io/zh/docs/v3.4/pluggable-components/)。

### KubeSphere DevOps 系统

基于 [Jenkins](https://jenkins.io/) 的 KubeSphere DevOps 系统是专为 Kubernetes 中的 CI/CD 工作流设计的，它提供了一站式的解决方案，帮助开发和运维团队用非常简单的方式构建、测试和发布应用到 Kubernetes。它还具有插件管理、[Binary-to-Image (B2I)](https://www.kubesphere.io/zh/docs/v3.4/project-user-guide/image-builder/binary-to-image/)、[Source-to-Image (S2I)](https://www.kubesphere.io/zh/docs/v3.4/project-user-guide/image-builder/source-to-image/)、代码依赖缓存、代码质量分析、流水线日志等功能。

DevOps 系统为用户提供了一个自动化的环境，应用可以自动发布到同一个平台。它还兼容第三方私有镜像仓库（如 Harbor）和代码库（如 GitLab/GitHub/SVN/BitBucket）。它为用户提供了全面的、可视化的 CI/CD 流水线，打造了极佳的用户体验，而且这种兼容性强的流水线能力在离线环境中非常有用。

有关更多信息，请参见 [DevOps 用户指南](https://www.kubesphere.io/zh/docs/v3.4/devops-user-guide/)。

#### 在安装KubeSphere后启动 DevOps

1. 以 `admin` 用户登录控制台，点击左上角的**平台管理**，选择**集群管理**。

2. 点击**定制资源定义**，在搜索栏中输入 `clusterconfiguration`，点击搜索结果查看其详细页面。

   信息

   定制资源定义（CRD）允许用户在不新增 API 服务器的情况下创建一种新的资源类型，用户可以像使用其他 Kubernetes 原生对象一样使用这些定制资源。

3. 在**自定义资源**中，点击 `ks-installer` 右侧的更多中，选择**编辑 YAML**。

4. 在该 YAML 文件中，搜索 `devops`，将 `enabled` 的 `false` 改为 `true`。完成后，点击右下角的**确定**，保存配置。

```yaml
devops:
  enabled: true # 将“false”更改为“true”。
```

5.在 kubectl 中执行以下命令检查安装过程：

```shell
kubectl logs -n kubesphere-system $(kubectl get pod -n kubesphere-system -l 'app in (ks-install, ks-installer)' -o jsonpath='{.items[0].metadata.name}') -f
```

#### 不兼容的pod镜像包括以下

| 镜像名称              | 官方提供版本                                                 | 自测暂时可用版本                                             | DockerHub地址                                                |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| minio                 | docker.io/minio/minio:RELEASE.2019-08-07T01-59-21Z           | docker.io/minio/minio:RELEASE.2022-07-29T19-40-48Z           | https://hub.docker.com/layers/minio/minio/RELEASE.2022-07-29T19-40-48Z/images/sha256-cc1067cf3d480ffc8636784bde413e4c52575da6471833fb0d3b17d38cb3cefd?context=explore |
| mc                    | docker.io/minio/mc:RELEASE.2019-08-07T23-14-43Z              | docker.io/minio/mc:RELEASE.2022-07-29T19-17-16Z              | https://hub.docker.com/layers/minio/mc/RELEASE.2022-07-29T19-17-16Z/images/sha256-5849d1feb6d60e215175a12011d0be5580a79ed4251cd0f6c824a1a24332a28a?context=explore |
| argocd_applicationset | registry.cn-beijing.aliyuncs.com/kubesphereio/argocd-applicationset:v0.4.1 | docker.io/baloisemichaelmuehlebach/argocd-applicationset:v0.4.1 | https://hub.docker.com/layers/baloisemichaelmuehlebach/argocd-applicationset/v0.4.1/images/sha256-f6a0b73b48e92c9b9977bde40c360c9b813c06845a7526469186dc7c2bb3a75f?context=explore |
| jenkins               | registry.cn-beijing.aliyuncs.com/kubesphereio/ks-jenkins:v3.4.0-2.319.3-1 | docker.io/kubesphere/ks-jenkins:v3.4.1-2.319.3               | https://hub.docker.com/layers/kubesphere/ks-jenkins/v3.4.1-2.319.3/images/sha256-15a40d2da981451e903e1a59f5e6963e4291f20153eddb3d922dd965ccc1f6d3?context=explore |
| default-http-backend  | registry.cn-beijing.aliyuncs.com/kubesphereio/defaultbackend-arm64:1.4 | mirrorgooglecontainers/defaultbackend-arm64:1.4              | https://hub.docker.com/layers/mirrorgooglecontainers/defaultbackend-arm/1.4/images/sha256-f02490f65b15e347661216d55b8fc0b5d7e472aa0b1ef9241be4eea880a3c594?context=explore |

![image-20231027094147632](https://lsky.hhdxw.top/imghub/2023/10/image-202310271698370908.png)

#### 更换方式

##### 1.直接在开启devops配置中设置仓库以及标签

直接在上文中`ks-installer`**编辑 YAML**文件中添加Docker的仓库（repository）与标签（tag）信息

![image-20231027102441263](https://lsky.hhdxw.top/imghub/2023/10/image-202310271698373481.png)

```yaml
spec:
  mc_tag: RELEASE.2022-07-29T19-17-16Z
  mc_repo: docker.io/minio/mc
  minio_repo: docker.io/minio/minio
  minio_tag: RELEASE.2022-07-29T19-40-48Z
  argocd_applicationset_repo: docker.io/baloisemichaelmuehlebach/argocd-applicationset
  argocd_applicationset_tag:v0.4.1
  jenkins_repo: docker.io/kubesphere/ks-jenkins
  jenkins_tag: v3.4.1-2.319.3
```

##### 2.创建项目后，在项目中**编辑 YAML**中重新创建

1. 以 `admin` 用户登录控制台，点击左上角的**平台管理**，选择**集群管理**。

2. 点击**应用负载**下级目录**工作负载**，查看项目状态异常的项目点击进入项目。

3. 在**更多操作**中，选择**编辑 YAML**，找到如下内容更换完成并保存。

   ```yaml
   image: >-
     registry.cn-beijing.aliyuncs.com/kubesphereio/ks-jenkins:v3.4.0-2.319.3-1
   #上述内容更换为下面，此处只举例，具体参考上面的表格
   image: 'docker.io/kubesphere/ks-jenkins:v3.4.1-2.319.3'
   ```

4. 系统会自动启动新的副本，稍作等待副本完成后自动会删除错误的副本，如果没有启动可以在**更多操作**中，选择**重新创建**。

5. 另外可以在修改记录中查看修改对比记录

   ![image-20231027102325546](https://lsky.hhdxw.top/imghub/2023/10/image-202310271698373405.png)

#### 注意：

本人确认正在使用上述特定版本号的服务，并确认在目前的使用过程中暂未出现问题。然而，这在任何程度上都不能被视为是对您个人使用此版本的保证或建议。

请您知悉，上述版本号仅供参考，并不能保证与所有的硬件、软件环境或与所有的使用场景完全兼容。在您决定使用该版本时，您需要自行评估可能存在的风险，其中可能包括但不限于功能不全、系统不稳定、可能存在的安全问题等情况。

对于因对我个人版本号使用情况的借鉴引发的任何形式的损失或问题，我个人以及相关的产品或服务提供者无需负担任何责任。如果您在使用过程中遇到任何问题，建议直接联系相关的产品或服务的提供者以寻求技术支持。

#### 附录：

新版本的`minio`会让部分功能无法使用，`RELEASE.2022-10-29T06-21-33Z`版本以后可能不兼容。

![image-202310271698374117](https://lsky.hhdxw.top/imghub/2023/10/image-202310271698374117.png)

#### 验证组件的安装

执行以下命令来检查容器组的状态：

```shell
kubectl get pod -n kubesphere-devops-system
```

如果组件运行成功，输出结果如下：

```shell
NAME                          READY   STATUS    RESTARTS   AGE
devops-jenkins-5cbbfbb975-hjnll   1/1     Running   0          40m
s2ioperator-0                 1/1     Running   0          41m
```

