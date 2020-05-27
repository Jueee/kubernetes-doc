### 问题解决

#### 问题：k8s 打包镜像失败

```
Failed to pull image "XXX": rpc error: code = Unknown desc = Error response from daemon: pull access denied for XXX, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
```

解决

##### 方法一

```
$ sudo mkdir /var/lib/kubelet
$ sudo cp ~/.docker/config.json /var/lib/kubelet/config.json
```

##### 方法二

[参考资料](https://kubernetes.io/docs/concepts/containers/images/#configuring-nodes-to-authenticate-to-a-private-repository)

```
$ kubectl create secret docker-registry registry-harbor --namespace=default \
    --docker-server=XXX --docker-username=XXX \
    --docker-password=XXX --docker-email=XXX
    
$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
registry-harbor       kubernetes.io/dockerconfigjson        1      2s
```

方法三：

[docs-kubernetes-secret.md](docs-kubernetes-secret.md)

方法四：



#### 问题：PersistentVolume 失效

```
The PersistentVolume "mysqldata" is invalid: spec.persistentvolumesource: Forbidden: is immutable after creation
```

原因：当原来的PV或PVC还在，而你又创建了一个新的PV, 并与原来的重名，则会得到该错误。

解决：将原来的PV或PVC删掉，再重新创建新的。

```
$ kubectl delete pvc mysqldata -n testnamespace
```



