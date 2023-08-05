## 1.Kubernetes简述和特性

### 1.1 k8s简述

- k8s是谷歌在2014年开源的容器化集群管理系统
- 使用k8s进行容器化应用部署
- 使用k8s利于应用扩展
- k8s目标是是让部署容器化应用更简洁和高效



### 1.2 k8s功能

1. 自动装箱
2. 自动修复(自愈能力)
3. 水平扩展
4. 服务发现
5. 滚动更新
6. 版本回退
7. 密钥和配置管理(类似热部署)
8. 存储编排
9. 批处理

### 1.3 k8s集群架构组件

Master(主控节点)和Node(工作节点)

(1) master组件

- apiserver

集群统一入口，以restful方式，交给etcd存储

- scheduler

节点调度，选择node节点应用部署

- controller-manager

处理集群中常规后台任务，一个资源对应一个控制器

- etcd

存储系统,用于保存集群相关的数据

(2) node组件

- kubeelet

master派到node节点的代表，管理本机容器

- kube-proxy

提供网络代理，实现负载均衡等操作

## 

### 1.4 k8s核心概念

1. Pod

- 最小部署单元
- 一组容器的集合
- 共享网络
- 生命周期是短暂的

1. Controller

- 确保预期的pod副本数量
- 无状态应用部署
- 有状态应用部署
- 确保所有的node运行同一个pod
- 一次性任务和定时任务

1. Service

- 定义一组pod的访问规则



- **Master（主节点）：** 控制 Kubernetes 节点的机器，也是创建作业任务的地方。
- **Node（节点）：** 这些机器在 Kubernetes 主节点的控制下执行被分配的任务。
- **Pod：** 由一个或多个容器构成的集合，作为一个整体被部署到一个单一节点。同一个 pod 中的容器共享 IP 地址、进程间通讯（IPC）、主机名以及其它资源。Pod 将底层容器的网络和存储抽象出来，使得集群内的容器迁移更为便捷。
- **Replication controller（复制控制器）：** 控制一个 pod 在集群上运行的实例数量。
- **Service（服务）：** 将服务内容与具体的 pod 分离。Kubernetes 服务代理负责自动将服务请求分发到正确的 pod 处，不管 pod 移动到集群中的什么位置，甚至可以被替换掉。
- **Kubelet：** 这个守护进程运行在各个工作节点上，负责获取容器列表，保证被声明的容器已经启动并且正常运行。
- **kubectl：** 这是 Kubernetes 的命令行配置工具



### 1.5 k8s集群搭建

1. 搭建环境平台规划

- 单master集群

![img](./k8s.assets/1690116032765-7e864076-ecab-4f3a-a863-4a31318be558.png)

缺点:master挂掉就寄了

- 多master集群

![img](./k8s.assets/1690116111043-7fa6e742-7d8d-42ec-b6a4-6d3a59250d7a.png)

高可用的集群

1. 服务器硬件配置要求

- 测试环境：

master: 2核 4G 20G

node: 4核 8G 40G

- 生产环境：

更高要求

1. 搭建k8s集群部署方式

- kubeadm

一个k8s部署工具，提供kubeadmin init 和kubeadm josin，用于快速部署kubernetes集群

- 二进制包

从github下载发行版二进制包，各个组件要单独部署



#### 1.5.1 kubeadm方式搭建

```bash
# 设置服务器hostname
$ hostnamectl set-hostname <hostname>
$ yum install -y kubelet-1.18.0 kubeadm-1.18.0 kubectl-1.18.0
# 设置开机启动
$ systemctl enable kebelet
# 部署Kubernetes Master,在master节点上执行
# 由于默认拉取镜像地址 k8s.gcr.io 国内无法访问，这里指定阿里云镜像仓库地址
$ kubeadm init \
--apiserver-advertise-address=192.168.31.61 \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version v1.17.0 \
--service-cidr=10.96.0.0/12 \
--pod-network-cidr=10.244.0.0/16
# 使用 kubectl 工具
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
# 查看目前的节点
$ kubectl get nodes
# 加入 Kubernetes Node
# 在 192.168.31.62/63（Node节点而不是master节点）执行
# 向集群添加新节点，执行在 kubeadm init 输出的 kubeadm join 命令
$ kubeadm join 192.168.31.61:6443 --token esce21.q6hetwm8si29qxwn \
--discovery-token-ca-cert-hash
sha256:00603a05805807501d7181c3d60b478788408cfe6cedefedb1f97569708be9c5
# 安装 Pod 网络插件
$ kubectl apply –f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
# 测试 kubernetes 集群
# 在 Kubernetes 集群中创建一个 pod，验证是否正常运行：
$ kubectl create deployment nginx --image=nginx
$ kubectl expose deployment nginx --port=80 --type=NodePort
$ kubectl get pod,svc
```



1. 安装3台虚拟机，安装操作系统
2. 对三个安装之后的操作系统进行初始化操作
3. 在三个节点安装docker kubelet,kubeadm kubectl
4. 在master节点执行kubeadm init命令进行初始化
5. 在node节点上执行kubeadm join命令把node节点添加到当前集群里面



#### 1.5.2 二进制包搭建

![img](./k8s.assets/1690270222280-22955cfc-68fa-47e9-b935-2b34ba6d3b16.png)



1. 创建多台虚拟机，安装Linux操作系统
2. 操作系统初始化
3. 为etcd和apiserver自签证书
4. 部署etcd集群
5. 部署master组件

kube-apiserver,kube-controller-manager,kube-scheduler,etcd

1. 部署node组件

kebelet,kube-proxy,docker,etcd

1. 部署集群网络



### 1.6 k8s集群命令行工具kubectl

kubectl是Kubernetes集群的命令行工具,通过kubectl能够对集群本身进行管理，并能够在集群上进行容器化应用的安装部署

```bash
$ kubectl [command] [type] [name] [flags]
$ kubectl --help
```