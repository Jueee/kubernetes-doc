## 安装 kubernetes helm

### 安装 helm

```shell
$ wget https://get.helm.sh/helm-v3.2.1-linux-amd64.tar.gz
$ tar -zxvf helm-v3.2.1-linux-amd64.tar.gz
$ cd linux-amd64/
$ cp helm /usr/local/bin/
$ cp tiller /usr/local/bin/
```

查看版本

```
$ helm version
version.BuildInfo{Version:"v3.2.1", GitCommit:"fe51cd1e31e6a202cba7dead9552a6d418ded79a", GitTreeState:"clean", GoVersion:"go1.13.10"}
```

### 配置仓库

```
$ helm repo add aliyun https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
$ helm repo update
$ helm repo list
NAME            URL
aliyun          https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
```

- 微软仓库（http://mirror.azure.cn/kubernetes/charts/）强烈推荐，基本上官网有的chart这里都有。
- 阿里云仓库（https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts ）
- 官方仓库（https://hub.kubeapps.com/charts/incubator）官方chart仓库，国内有点不好使。

### 相关资料

GitHub

> https://github.com/helm/helm

