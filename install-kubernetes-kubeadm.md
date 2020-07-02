## 通过 kubeadm 安装 kubernetes

### 相关资料

官方教程

> https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

### 安装步骤

##### 关闭swap

swap的作用类似Windows系统下的“虚拟内存”

```shell
# 永久关闭swap分区
$ sudo swapoff -a 
$ sudo sed -i 's/.*swap.*/#&/' /etc/fstab
```

##### 安装 kubeadm

安装 kubeadm、kubelet 和 kubectl

```shell
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

##### 版本查看

```shell
$ kubeadm version
kubeadm version: &version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.3", GitCommit:"2e7996e3e2712684bc73f0dec0200d64eec7fe40", GitTreeState:"clean", BuildDate:"2020-05-20T12:49:29Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}
```

##### 启动k8s

```shell
$ kubeadm init
```

##### 设置config

To start using your cluster, you need to run the following as a regular user:

```shell
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

版本查看

```shell
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.3", GitCommit:"2e7996e3e2712684bc73f0dec0200d64eec7fe40", GitTreeState:"clean", BuildDate:"2020-05-20T12:52:00Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.3", GitCommit:"2e7996e3e2712684bc73f0dec0200d64eec7fe40", GitTreeState:"clean", BuildDate:"2020-05-20T12:43:34Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}
```

### 问题解决

一、that the pod didn't tolerate.

创建单机版的 k8s 时，这个时候 master 节点是默认不允许调度 pod 的，我们需要执行如下这个命令将 master 标记为可调度.

允许master节点部署pod，使用命令如下:

```shell
$ kubectl taint nodes --all node-role.kubernetes.io/master-
node/debian untainted
```

禁止master部署pod

```shell
$ kubectl taint nodes --all node-role.kubernetes.io/master=true:NoSchedule
node/debian tainted
```

[参考资料](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#master-isolation)

二、启动报错：Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "kubernetes")

没有执行脚本程序

```shell
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

