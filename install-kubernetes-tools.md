## 使用kubens快速切换namespace

#### 安装

```shell
git clone https://github.com/ahmetb/kubectx
sudo cp kubectx/kube* /usr/local/bin/
```
#### 使用

```shell
$ kubens
default
kube-public
kube-system
```

切换到指定namespace

```shell
$ kubens kube-system
```

在最近使用过的2个namespace快速切换：

```shell
$ kubens -
```

