# 多节点部署Kubernetes集群

[TOC]

## 编辑环境

将主机名指向本机IP，**主机名只能包含：字母、数字、-（横杠）、.（点）**

### 获取主机名

```shell
hostname
```

#### 临时设置主机名

```shell
hostname 主机名
```

#### 永久设置主机名

```shell
sudo echo '主机名' > /etc/hostname
```

#### 编辑 hosts

```shell
sudo vim /etc/hosts
```

```shell
10.211.55.20 k8s-node1 #集群主节点
10.211.55.21 k8s-node2 #集群node节点
10.211.55.19 k8s-node3 #集群node节点
```

### 同步时间

```shell
sudo yum -y install ntpdate
sudo ntpdate ntp1.aliyun.com
sudo systemctl status ntpdate
sudo systemctl start ntpdate
sudo systemctl status ntpdate
sudo systemctl enable ntpdate
```

Centos9中ntp 软件包已被替换为 chrony

```shell
sudo yum -y install chrony
sudo systemctl start chronyd
sudo systemctl enable chronyd
sudo systemctl restart chronyd
sudo chronyc makestep
date
```

### 安装并配置 bash-completion，添加命令自动补充

```shell
sudo yum -y install bash-completion
source /etc/profile
```

### 关闭防火墙

```shell
sudo systemctl stop firewalld.service 
sudo systemctl disable firewalld.service
```

### 开通全部端口

```shell
firewall-cmd --zone=public --add-port=1-65535/udp --permanent
firewall-cmd --zone=public --add-port=1-65535/tcp --permanent
firewall-cmd --reload
```

### 打开端口转发

```shell
firewall-cmd --add-masquerade --permanent # 端口转发
firewall-cmd --reload
firewall-cmd --list-all
firewall-cmd --list-all --zone=trusted
```

### 关闭交换空间

```shell
free -h
sudo swapoff -a
sudo sed -i 's/.*swap.*/#&/' /etc/fstab
free -h
```

### 关闭 selinux

```shell
getenforce
cat /etc/selinux/config
sudo setenforce 0
sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
cat /etc/selinux/config
```

## 安装Docker

### 卸载旧Docekr

```shell
sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine
```

### 更新一下环境：

```
yum -y update
```

### 安装一些必要的系统工具：

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

### 添加软件源信息：

```
sudo yum install -y yum-utils
```

```
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo #国内阿里源
```

```
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo #docker官方镜像源
```

### 安装 Docker

```
sudo yum install docker-ce docker-ce-cli containerd.io
```

### 启动 Docker 后台服务

```
sudo systemctl start docker
sudo systemctl enable docker
```

### 测试运行 hello-world

```
docker run hello-world
```

## 编辑Kubernetes环境

### 修改containerd

```shell
 #停止containerd
 sudo systemctl stop containerd.service
 
 sudo cp /etc/containerd/config.toml /etc/containerd/config.toml.bak
 sudo cp /etc/containerd/config.toml /etc/containerd/config.toml.bak
 sudo containerd config default > $HOME/config.toml
 sudo cp $HOME/config.toml /etc/containerd/config.toml
```

```shell
vi /etc/containerd/config.toml
```

 ```shell
 # 修改 /etc/containerd/config.toml 文件后，要将 docker、containerd 停止后，再启动
 sudo sed -i "s#registry.k8s.io/pause#registry.aliyuncs.com/google_containers/pause#g" /etc/containerd/config.toml
# https://kubernetes.io/zh-cn/docs/setup/production-environment/container-runtimes/#containerd-systemd
# 确保 /etc/containerd/config.toml 中的 disabled_plugins 内不存在 cri
sudo sed -i "s#SystemdCgroup = false#SystemdCgroup = true#g" /etc/containerd/config.toml
 ```

```shell
sudo systemctl enable --now containerd.service
# sudo systemctl status containerd.service

# sudo systemctl status docker.service
sudo systemctl start docker.service
# sudo systemctl status docker.service
sudo systemctl enable docker.service
sudo systemctl enable docker.socket
sudo systemctl list-unit-files | grep docker
```

```shell
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors":[
			  "https://9kkc1zdn.mirror.aliyuncs.com",
              "https://hub-mirror.c.163.com/",
              "https://docker.mirrors.ustc.edu.cn/",
              "https://registry.docker-cn.com"],
  "exec-opts": ["native.cgroupdriver=systemd"]
}
EOF
```

### 重启

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
sudo docker info

sudo systemctl status docker.service
sudo systemctl status containerd.service
```

### 添加阿里云镜像仓库（注意其中电脑版本例如：aarch64、x86_64、ppc64le、armhfp、390x）

```shell
# 文档：https://developer.aliyun.com/mirror/kubernetes

cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-aarch64/
# 是否开启本仓库
enabled=1
# 是否检查 gpg 签名文件
gpgcheck=0
# 是否检查 gpg 签名文件
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg

EOF
```

## 安装K8s依赖

```shell
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter
```

```shell
# 设置所需的 sysctl 参数，参数在重新启动后保持不变
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1

EOF

# 应用 sysctl 参数而不重新启动
sudo sysctl --system
```

```shell
# 通过运行以下指令确认 br_netfilter 和 overlay 模块被加载：
lsmod | grep br_netfilter
lsmod | grep overlay
```

```shell
# 通过运行以下指令确认 net.bridge.bridge-nf-call-iptables、net.bridge.bridge-nf-call-ip6tables 和 net.ipv4.ip_forward 系统变量在你的 sysctl 配置中被设置为 1：
sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward
```

```shell
#1.27.3版本
sudo yum install -y kubelet-1.27.3-0 kubeadm-1.27.3-0 kubectl-1.27.3-0 --disableexcludes=kubernetes --nogpgcheck

# 安装最新版
# sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes --nogpgcheck
systemctl daemon-reload
sudo systemctl restart kubelet
sudo systemctl enable kubelet
```

### 查看kubelet日志

```shell
# k8s 未初始化时，kubelet 可能无法启动
journalctl -xefu kubelet
```

### 查看kubelet状态

```shell
# k8s 未初始化时，kubelet 可能无法启动
sudo systemctl status kubelet
```

## 以上命令所有节点均相同

## 控制面板：初始化（主节点）

```shell
kubeadm init --image-repository=registry.aliyuncs.com/google_containers
# 指定集群的IP
# kubeadm init --image-repository=registry.aliyuncs.com/google_containers --apiserver-advertise-address=192.168.80.60

# --apiserver-advertise-address：API 服务器所公布的其正在监听的 IP 地址。如果未设置，则使用默认网络接口。存在多个网卡时推荐设置此参数
# --pod-network-cidr：指明 pod 网络可以使用的 IP 地址段。如果设置了这个参数，控制平面将会为每一个节点自动分配 CIDRs。
# --service-cidr：默认值："10.96.0.0/12"，为服务的虚拟 IP 地址另外指定 IP 地址段

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# 或者在环境变量中添加：export KUBECONFIG=/etc/kubernetes/admin.conf
# 添加完环境变量后，刷新环境变量：source /etc/profile

kubectl cluster-info

# 初始化失败后，可进行重置，重置命令：kubeadm reset

# 执行成功后，会出现类似下列内容：
#kubeadm join 10.211.55.20:6443 --token opnt8d.m1db0aosc8q7013p \
#        --discovery-token-ca-cert-hash sha256:bf4601f8a84e77cd4a67ad1cfbf59be5c5aac7cf5e2efc16809a7a044389913e 

#
# kubeadm token create --print-join-command
```

## node 节点：加入集群(子节点操作)

```shell
# 运行的内容来自上方执行结果
kubeadm join 10.211.55.20:6443 --token opnt8d.m1db0aosc8q7013p \
        --discovery-token-ca-cert-hash sha256:bf4601f8a84e77cd4a67ad1cfbf59be5c5aac7cf5e2efc16809a7a044389913e 


# kubeadm token create --print-join-command
```

## 控制面板

```shell
kubectl get pods --all-namespaces -o wide
```

```shell
[root@localhost ~]# kubectl get pods --all-namespaces -o wide
NAMESPACE     NAME                                READY   STATUS    RESTARTS   AGE   IP             NODE        NOMINATED NODE   READINESS GATES
kube-system   coredns-7bdc4cb885-h685f            0/1     Pending   0          71s   <none>         <none>      <none>           <none>
kube-system   coredns-7bdc4cb885-rrlbq            0/1     Pending   0          71s   <none>         <none>      <none>           <none>
kube-system   etcd-k8s-node1                      1/1     Running   0          76s   10.211.55.20   k8s-node1   <none>           <none>
kube-system   kube-apiserver-k8s-node1            1/1     Running   0          78s   10.211.55.20   k8s-node1   <none>           <none>
kube-system   kube-controller-manager-k8s-node1   1/1     Running   0          76s   10.211.55.20   k8s-node1   <none>           <none>
kube-system   kube-proxy-64rdg                    1/1     Running   0          71s   10.211.55.20   k8s-node1   <none>           <none>
kube-system   kube-scheduler-k8s-node1            1/1     Running   0          78s   10.211.55.20   k8s-node1   <none>           <none>
```

```shell
kubectl get nodes -o wide
```

```shell
[root@localhost ~]# kubectl get nodes -o wide
NAME        STATUS     ROLES           AGE    VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE          KERNEL-VERSION           CONTAINER-RUNTIME
k8s-node1   NotReady   control-plane   2m7s   v1.27.3   10.211.55.20   <none>        CentOS Stream 9   5.14.0-373.el9.aarch64   containerd://1.6.24
```

### 控制面板：配置网络，选择 Calico 配置

归档文档：https://docs.tigera.io/archive/

| **Kubernetes 版本**    | **Calico 版本**                                     | **Calico 文档**                                              | **Calico 配置**                                              |
| ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1.18、1.19、1.20       | 3.18                                                | https://docs.tigera.io/archive/v3.18/getting-started/kubernetes/requirements | https://docs.tigera.io/archive/v3.18/manifests/calico.yaml   |
| 1.19、1.20、1.21       | 3.19                                                | https://docs.tigera.io/archive/v3.19/getting-started/kubernetes/requirements | https://docs.tigera.io/archive/v3.19/manifests/calico.yaml   |
| 1.19、1.20、1.21       | 3.20                                                | https://docs.tigera.io/archive/v3.20/getting-started/kubernetes/requirements | https://docs.tigera.io/archive/v3.20/manifests/calico.yaml   |
| 1.20、1.21、1.22       | 3.21                                                | https://docs.tigera.io/archive/v3.21/getting-started/kubernetes/requirements | https://docs.tigera.io/archive/v3.21/manifests/calico.yaml   |
| 1.21、1.22、1.23       | 3.22                                                | https://docs.tigera.io/archive/v3.22/getting-started/kubernetes/requirements | https://docs.tigera.io/archive/v3.22/manifests/calico.yaml   |
| 1.21、1.22、1.23       | 3.23                                                | https://docs.tigera.io/archive/v3.23/getting-started/kubernetes/requirements | https://docs.tigera.io/archive/v3.23/manifests/calico.yaml   |
| 1.22、1.23、1.24、1.25 | 3.24                                                | https://docs.tigera.io/archive/v3.24/getting-started/kubernetes/requirements | https://docs.tigera.io/archive/v3.24/manifests/calico.yaml   |
| 1.22、1.23、1.24       | 3.25                                                | https://docs.tigera.io/archive/v3.25/getting-started/kubernetes/requirements | https://docs.tigera.io/archive/v3.25/manifests/calico.yaml   |
| 1.24、1.25、1.26、1.27 | 3.26（最新版，从 2023 年 5 月开始正式更新，未测试） | https://docs.tigera.io/calico/latest/getting-started/kubernetes/requirements | https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml |

```shell
# 下载 依赖
wget --no-check-certificate https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml 
```

```shell
# 修改 calico.yaml 文件
vim calico.yaml
```

```shell
# 在 - name: CLUSTER_TYPE 下方添加如下内容
- name: CLUSTER_TYPE
  value: "k8s,bgp"
  # 下方为新增内容
- name: IP_AUTODETECTION_METHOD
  value: "interface=网卡名称"

# 网卡名称通过ip addr进行查询
# INTERFACE_NAME=ens33
# sed -i '/k8s,bgp/a \            - name: IP_AUTODETECTION_METHOD\n              value: "interface=INTERFACE_NAME"' calico.yaml
# sed -i "s#INTERFACE_NAME#$INTERFACE_NAME#g" calico.yaml
```

```shell
# 配置网络
kubectl apply -f calico.yaml
```

### 控制面板：查看 pods、nodes

```shell
kubectl get nodes -o wide
kubectl get pods --all-namespaces -o wide
```

## k8s安装与配置已完成，下面内容是测试。

### 控制面板：创建 nginx 服务

```shell
# 带 命名空间、Service 的完整版参见：https://jihulab.com/xuxiaowei-cloud/xuxiaowei-cloud/-/blob/main/docs/deployment/nginx-deployment.yaml
cat > nginx.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.23.2
        ports:
        - containerPort: 80

EOF

cat nginx.yaml

kubectl apply -f nginx.yaml

# 编辑
# kubectl edit deployment nginx-deployment
```

### 查看服务

```shell
kubectl get pods --all-namespaces -o wide
kubectl get pods -o wide

# 控制面板：查看pod,svc
kubectl get pod,svc -o wide
```


```shell
# 控制面板：设置服务（将多个 nginx-deployment 的 pod 绑定在一起，通过一个 Service 端口统一对外提供）
kubectl expose deployment nginx-deployment --type=NodePort --name=nginx-service

# 带 命名空间、Service 的完整版参见：https://jihulab.com/xuxiaowei-cloud/xuxiaowei-cloud/-/blob/main/docs/deployment/nginx-deployment.yaml
```

### 再次查看

```shell
# 控制面板：查看pod,svc
kubectl get pod,svc -o wide
```

### 删除部署

```shell
kubectl delete -f nginx.yaml
```

```shell
kubectl delete deployment pod名称
```

## 其他辅助

### CentOS 命令自动补充

1. 安装 bash-completion

    ```shell
    yum install -y bash-completion
    ```

2. 拷贝 kubernetes 的自动补全脚本到系统补全目录中

    ```shell
    source <(kubectl completion bash)
    echo "source <(kubectl completion bash)" >> ~/.bashrc
    ```

3. 重新加载环境变量，使设置生效

    ```shell
    source ~/.bashrc
    ```

### Token 相关命令

1. 控制平面节点上运行以下命令来获取令牌

    ```shell
    kubeadm token list
    ```

2. 默认情况下，令牌会在 24 小时后过期，可以通过在控制平面节点上运行以下命令来创建新令牌

    ```shell
    kubeadm token create
    ```

### 相关命令

1. 查看更多信息

    ```shell
     -o wide
    ```

2. 查看所有命名空间

    ```shell
    --all-namespaces
    ```

3. 查看指定命名空间

    ```shell
    -n 命名空间
    ```

4. 查看所有 pod

    ```shell
    kubectl get pods --all-namespaces -o wide
    ```

5. 查看 pod 描述

    ```shell
    kubectl -n 命名空间 describe pod 名称
    ```

6. 删除 pod

    ```shell
    kubectl -n 命名空间 delete pod 名称
    ```

7. 进入 pod

    ```shell
    kubectl exec -it pod名称 bash
    ```

8. 查看 Service Account

    ```shell
    kubectl get sa --all-namespaces
    ```

    ```shell
    kubectl -n 命名空间 get sa
    ```

9. 查看 pv

    ```shell
    kubectl get pv
    ```

10. 查看 pvc

    ```shell
    kubectl get pvc
    ```

11. 查看角色绑定

    ```shell
    kubectl get rolebinding --all-namespaces -o wide
    ```

## 常见报错

### 子节点环境变量没有配置

```shel
[root@k8s-node2 ~]# kubectl get nodes
E1020 09:02:20.228531    3007 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp [::1]:8080: connect: connection refused
E1020 09:02:20.228894    3007 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp [::1]:8080: connect: connection refused
E1020 09:02:20.230721    3007 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp [::1]:8080: connect: connection refused
E1020 09:02:20.232525    3007 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp [::1]:8080: connect: connection refused
E1020 09:02:20.234259    3007 memcache.go:265] couldn't get current server API group list: Get "http://localhost:8080/api?timeout=32s": dial tcp [::1]:8080: connect: connection refused
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

首先先进入`/etc/kubernetes`文件夹下面然后查看你的配置文件

1.27版本使用的名称为`kubelet.conf`以前版本应该是`admin.conf`

配置环境变量

```shell
echo "export KUBECONFIG=/etc/kubernetes/kubelet.conf" >> ~/.bash_profile
 
source ~/.bash_profile
```

现在就已经正常了

