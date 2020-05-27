## Kubernetes 学习资料

安装资料：

- [安装 kubectl 客户端](install-kubernetes-kubectl.md)
- [通过 Minikube 安装 Kubernetes](install-kubernetes-minikube.md)
- [通过 kubeadm 安装 kubernetes](install-kubernetes-kubeadm.md)

学习资料：

- [学习 Kubernetes 基础知识](learn-kubernetes-basics.md)
- [通过命令行部署 nginx 镜像](learn-nginx-command.md)
- [通过配置文件部署 nginx 镜像](learn-nginx-yml.md)

组件相关：

- [yml 配置文件相关](docs-kubernetes-yml.md)
- [namespaces 相关](docs-kubernetes-namespaces.md)
- [secret 相关](docs-kubernetes-secret.md)

#### 组件关系总结

1. 用户通过 kubectl 创建 Deployment 。
2. Deployment 创建 ReplicaSet 。
3. ReplicaSet 创建 Pod 。

![1590478941730](assets/1590478941730.png)