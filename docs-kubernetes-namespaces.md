## k8s namespaces 相关文档

命令行创建 namespace

```
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

