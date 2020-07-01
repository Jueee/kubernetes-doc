## kubernetes node 相关文档

#### 查看 node

```
kubectl get node
```

#### 指定 node

给node加标签

```
kubectl label nodes k8s-slave2 slave=184
```

查看标签

```
$ kubectl describe node k8s-slave2
```

指定NodeSelector

```yml
		spec:
            nodeSelector:
                slave: "33"
            containers:
              - name: elasticsearch
                image: elasticsearch:7.4.2
```

