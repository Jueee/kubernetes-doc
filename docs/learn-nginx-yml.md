#### 运行 nginx deployment

##### 编写 yml

nginx-deployment.yml

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: nginx-deployment
spec:
    replicas: 2
    selector:
        matchLabels:
            app: nginx-deployment
    template:
        metadata:
            labels:
                app: nginx-deployment
        spec:
            containers:
              - name: nginx
                image: nginx
```

##### 运行 yml

kubectl create命令，是先删除所有现有的东西，重新根据yaml文件生成新的。

所以要求yaml文件中的配置必须是完整的。

```shell
$ kubectl create -f nginx-deployment.yml
deployment.apps/nginx-deployment created
```

kubectl apply命令，根据配置文件里面列出来的内容，升级现有的。

所以yaml文件的内容可以只写需要升级的属性。

```shell
$ kubectl apply -f nginx-deployment.yml
deployment.apps/nginx-deployment created
```

##### 删除资源

```shell
$ kubectl delete -f nginx-deployment.yml
deployment.apps "nginx-deployment" deleted
```

#### 运行 nginx pod

##### 编写 yml

nginx-pod.yml

```yml
apiVersion: v1
kind: Pod
metadata:
    name: nginx-pod
spec:
    containers:
      - name: nginx
        image: nginx
```

##### 运行 yml

```shell
$ kubectl apply -f nginx-pod.yml
pod/nginx-pod created
```

##### 查看资源

```shell
$ kubectl get pod
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          2m
```

##### 删除资源

```shell
$ kubectl delete -f nginx-pod.yml
pod "nginx-pod" deleted
```

#### 通过 Dashboard 部署

查看

```shell
$ kubectl get deployments -A
NAMESPACE              NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
default                nginx-deployment            2/2     2            2           77m
```

删除

```shell
$ kubectl delete deployments nginx-deployment --namespace=default
deployment.apps "nginx-deployment" deleted
```


