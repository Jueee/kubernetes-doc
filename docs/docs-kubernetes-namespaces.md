## k8s namespaces 相关文档

#### 创建 namespace

命令行创建 namespace

```shell
$ kubectl create namespace testnamespace 
```

配置文件创建 namespace

```yml
apiVersion: v1
kind: Namespace
metadata:
    name: testnamespace
    labels:
        name: testnamespace
```

#### 切换 namespace

切换到指定 namespace

```shell
$ kubens kube-system
```

过以下命令在最近使用过的2个namespace快速切换：

```shell
$ kubens -
```

