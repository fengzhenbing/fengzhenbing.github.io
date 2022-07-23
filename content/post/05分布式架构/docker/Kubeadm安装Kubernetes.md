---
title: "Kubeadm安装Kubernetes"
date: 2021-06-08
description: Kubeadm安装Kubernetes
tags:
    - 微服务
    - 云原生
    - K8S
categories:
    - 云原生
    - 微服务
    - K8S
---





## 1.环境准备

安装 Kubernetes 最小需要 2 核处理器、2 GB 内存，且为 x86 架构（暂不支持 ARM 架构)

本次实验操作系统：ubantu 20.04LTS 

 Kubernetes 并不在主流 Debian 系统自带的软件源中，所以要手工注册，然后才能使用`apt-get`安装

```shell
# 添加GPG Key
$ sudo curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -

# 添加K8S软件源
$ sudo add-apt-repository "deb https://apt.kubernetes.io/ kubernetes-xenial main"
```

如果不能科学上网，可以使用阿里云的软件源地址

```shell
# 添加GPG Key
$ curl -fsSL http://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | sudo apt-key add -

# 添加K8S软件源
$ sudo add-apt-repository "deb http://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main"
```

添加源后需要更新

```shell
sudo apt-get update
```



## 2. 安装 **kubelet**、**kubectl**、**kubeadm**

官网介绍：https://kubernetes.io/docs/reference/setup-tools/kubeadm/

- kubeadm: 引导启动 Kubernate 集群的命令行工具。
- kubelet: 在群集中的所有计算机上运行的组件, 并用来执行如启动 Pods 和 Containers 等操作。
- kubectl: 用于操作运行中的集群的命令行工具。



### 2.1关闭Swap 分区

 kubeadm 初始化集群之前，需要关闭Swap 分区，首先基于安全性（如在官方文档中承诺的 Secret 只会在内存中读写，不会落盘）、利于保证节点同步一致性等原因，从 1.8 版开始，Kubernetes 就在它的文档中明确声明了它默认**不支持**Swap 分区，在未关闭 Swap 分区的机器中，集群将直接无法启动；

其他参考

> 主要有两个方面原因：
>
> 第一是因为性能问题，在生产环境我们经常会遇到容器性能突然降低的情况，查看原因后，大部分都是因为开启了swap导致的。swap看似解决了有限内存的问题，但这种通过时间换空间的做法也给性能带来了很大问题，尤其是在高并发场景中，很容易导致系统不稳定。
>
> 第二是因为k8s定义的资源模型中，CPU和内存都是确定的可用资源，在调度的时候都会考虑在内。比如，设置了内存设置了limit 2G，就代表最大可用内存是2G，而引入swap（cgroup支持swap限制）后这个模型就变得复杂了，而且需要结合Qos，swap的使用完全是由操作系统根据水位自行调节的，并不直接受kubelet管理



一次性关闭

```shell
sudo swapoff -a
```

永久关闭

编辑器打开`/etc/fstab`，注释其中带有“swap”的行即可，

```shell
# 先备份
$ yes | sudo cp /etc/fstab /etc/fstab_bak
# 进行修改
$ sudo cat /etc/fstab_bak | grep -v swap > /etc/fstab
```



## 3 预拉取镜像

先查看kubeadm版本  v1.21.3

```shell
root@fengzhenbing-ubuntu:/home/fengzhenbing# kubeadm version
kubeadm version: &version.Info{Major:"1", Minor:"21", GitVersion:"v1.21.3", GitCommit:"ca643a4d1f7bfe34773c74f79527be4afd95bf39", GitTreeState:"clean", BuildDate:"2021-07-15T21:03:28Z", GoVersion:"go1.16.6", Compiler:"gc", Platform:"linux/amd64"}
```

首先使用以下命令查询当前版本需要哪些镜像：

```shell
fengzhenbing@fengzhenbing-ubuntu:/etc/docker$ kubeadm config images list --kubernetes-version v1.21.3
k8s.gcr.io/kube-apiserver:v1.21.3
k8s.gcr.io/kube-controller-manager:v1.21.3
k8s.gcr.io/kube-scheduler:v1.21.3
k8s.gcr.io/kube-proxy:v1.21.3
k8s.gcr.io/pause:3.4.1
k8s.gcr.io/etcd:3.4.13-0
k8s.gcr.io/coredns/coredns:v1.8.0
```

k8s.gcr.io 为google官方（Google Container Registry），对于不能科学上网的同学来说，可以使用阿里云加速

### 3.1 配置阿里云加速地址

阿里云加速地址获取 https://cr.console.aliyun.com/cn-shanghai/instances/mirrors

![image-20210725221057446](https://fengzhenbing.github.io/img/picgo/image-20210725221057446.png)

```shell
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://xxxx.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 3.2 拉取镜像，修改tag

对于通过`kubeadm config images list --kubernetes-version v1.21.3`查找到需要下载的镜像名称及版本后，可以从[DockerHub](https://hub.docker.com/)上找存有相同镜像的仓库来拉取。 我是用的[k8simage](https://hub.docker.com/r/k8simage/kube-proxy)的仓库，找到对应的v1.21.3版本的对应image

下面为从k8simage拉取到的镜像

```shell
fengzhenbing@fengzhenbing-ubuntu:/etc/docker$  sudo docker image list
REPOSITORY                         TAG        IMAGE ID       CREATED         SIZE
k8simage/kube-apiserver            v1.21.3    3d174f00aa39   9 days ago      126MB
k8simage/kube-scheduler            v1.21.3    6be0dc1302e3   9 days ago      50.6MB
k8simage/kube-controller-manager   v1.21.3    bc2bb319a703   9 days ago      120MB
k8simage/kube-proxy                v1.21.3    adb2816ea823   9 days ago      103MB
k8simage/pause                     3.4.1      0f8457a4c2ec   6 months ago    683kB
k8simage/etcd                      3.4.13-0   0369cf4303ff   11 months ago   253MB
k8simage/coredns                   1.7.0      bfe3a36ebd25   13 months ago   45.2MB
```

对每一镜像，1先拉取，2修改tag  3最后删除原来的。以kube-apiserver v1.21.3为例子

```shell
sudo docker pull k8simage/kube-apiserver:v1.21.3
sudo docker tag k8simage/kube-apiserver:v1.21.3 k8s.gcr.io/kube-apiserver:v1.21.3
sudo docker rmi k8simage/kube-apiserver:v1.21.3
```

上面全部操作结束后 可看到都更新为`kubeadm config images list --kubernetes-version v1.21.3`所需要的镜像了

```
root@fengzhenbing-ubuntu:/home/fengzhenbing# sudo docker image list
REPOSITORY                           TAG        IMAGE ID       CREATED         SIZE
k8s.gcr.io/kube-apiserver            v1.21.3    3d174f00aa39   9 days ago      126MB
k8s.gcr.io/kube-scheduler            v1.21.3    6be0dc1302e3   9 days ago      50.6MB
k8s.gcr.io/kube-proxy                v1.21.3    adb2816ea823   9 days ago      103MB
k8s.gcr.io/kube-controller-manager   v1.21.3    bc2bb319a703   9 days ago      120MB
quay.io/coreos/flannel               v0.14.0    8522d622299c   2 months ago    67.9MB
k8s.gcr.io/pause                     3.4.1      0f8457a4c2ec   6 months ago    683kB
k8s.gcr.io/etcd                      3.4.13-0   0369cf4303ff   11 months ago   253MB
k8s.gcr.io/coredns/coredns           v1.8.0     bfe3a36ebd25   13 months ago   45.2MB
```



## 4 初始化集群控制平面

* 确保 kubelet 是开机启动的

```
fengzhenbing@fengzhenbing-ubuntu:/etc/docker$ sudo systemctl start kubelet
fengzhenbing@fengzhenbing-ubuntu:/etc/docker$ sudo systemctl enable kubelet
```

* su 直接切换到 root 用户， 保证是root启动部署

```
kubeadm init \
  --pod-network-cidr=10.244.0.0/16 \
  --kubernetes-version v1.21.3 \
  --control-plane-endpoint 192.168.3.13
```

* 参数介绍

> `--kubernetes-version`参数（要注意版本号与 kubelet 一致）的目的是与前面预拉取是一样的，避免额外的网络访问去查询版本号；如果能够科学上网，不需要加这个参数。
>
> `--pod-network-cidr`参数是给Flannel网络做网段划分使用的，着在稍后介绍完 CNI 网络插件时会去说明。
>
> `--control-plane-endpoint`参数是控制面的地址，强烈建议使用一个域名代替直接的IP地址来建立Kubernetes集群。因为CA证书直接与地址相关，Kubernetes中诸多配置（配置文件、ConfigMap资源）也直接存储了这个地址，一旦更换IP，要想要不重置集群，手工换起来异常麻烦。所以最好使用hostname（仅限单节点实验）或者dns name。

执行完，如下提示表明成功



![image-20210725225837358](https://fengzhenbing.github.io/img/picgo/image-20210725225837358.png)



## 5 为当前用户生成 **kubeconfig**

使用 Kubernetes 前需要为当前用户先配置好 admin.conf 文件

```
root@fengzhenbing-ubuntu:~# mkdir -p $HOME/.kube
root@fengzhenbing-ubuntu:~# sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
root@fengzhenbing-ubuntu:~# sudo chown $(id -u):$(id -g) $HOME/.kube/config
```



## 6 CNI插件

CNI 即“容器网络接口”

部署 Kubernetes 时，我们可以有两种网络方案使得以后受管理的容器之间进行网络通讯：

- 使用 Kubernetes 的默认网络： 操作太复杂
- 使用 CNI 及其插件： 

Flannel 插件为比较推荐的

### 安装Flannel 插件

```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

https://raw.githubusercontent.com 无法访问时，可以找到该yml文件上传到服务器，如下执行

```
root@fengzhenbing-ubuntu:/home/fengzhenbing# kubectl apply -f kube-flannel.yml 
Warning: policy/v1beta1 PodSecurityPolicy is deprecated in v1.21+, unavailable in v1.25+
podsecuritypolicy.policy/psp.flannel.unprivileged created
clusterrole.rbac.authorization.k8s.io/flannel created
clusterrolebinding.rbac.authorization.k8s.io/flannel created
serviceaccount/flannel created
configmap/kube-flannel-cfg created
daemonset.apps/kube-flannel-ds created
```



```
$ kubectl taint nodes --all node-role.kubernetes.io/master-
```

## 7 **kubectl** 命令自动补全功能

```shell
$ echo 'source <(kubectl completion bash)' >> ~/.bashrc
$ echo 'source /usr/share/bash-completion/bash_completion' >> ~/.bashrc
```

## 8 加入其他 **Node** 节点到 **Kubernetes** 集群中

kubeadm init执行成功后的反馈内容中有提到：如上面的截图

```shell
You can now join any number of control-plane nodes by copying certificate authorities
and service account keys on each node and then running the following as root:

  kubeadm join 192.168.3.13:6443 --token qxu02l.g995k2yhkxjh3k80 \
        --discovery-token-ca-cert-hash sha256:b9887f55b739bd89d3b0dbb038e693df9b5b7d9759902ffb2a250288ba1ffc25 \
        --control-plane 

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.3.13:6443 --token qxu02l.g995k2yhkxjh3k80 \
        --discovery-token-ca-cert-hash sha256:b9887f55b739bd89d3b0dbb038e693df9b5b7d9759902ffb2a250288ba1ffc25 
```

Token 的有效时间为 24 小时，如果超时，使用以下命令重新获取：

```
$ kubeadm token create --print-join-command
```



## 9相关问题

无法访问api端点如下



![image-20210725233415235](https://fengzhenbing.github.io/img/picgo/image-20210725233415235.png)

对于实验测试非线上来讲，可以直接将`system:anonymous`加为用户

```
kubectl create clusterrolebinding test:anonymous --clusterrole=cluster-admin --user=system:anonymous
```

对于正式环境，需要创建一个用户并授权



## 