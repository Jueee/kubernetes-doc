## k8s secret 相关文档

Secret 解决了密码、token、密钥等敏感数据的配置问题，而不需要把这些敏感数据暴露到镜像或者 Pod Spec
中。Secret 可以以 Volume 或者环境变量的方式使用。

Secret 有三种类型：

- **Service Account** ：用来访问 Kubernetes API，由 Kubernetes 自动创建，并且会自动挂载到 Pod 的
  `/run/secrets/kubernetes.io/serviceaccount` 目录中
- **Opaque** ：base64编码格式的Secret，用来存储密码、密钥等
- **kubernetes.io/dockerconfigjson** ：用来存储私有 docker registry 的认证信息

### 基于现有Docker凭据

#### 创建secret

创建单个私有仓库认证

```shell
$ kubectl create secret docker-registry myregistrykey \
	--docker-server=DOCKER_REGISTRY_SERVER \
	--docker-username=DOCKER_USER \
	--docker-password=DOCKER_PASSWORD \
	--docker-email=DOCKER_EMAIL
secret "myregistrykey" created
```

创建当前服务器上的所有认证

```shell
$ kubectl create secret generic harborsecret    \
	--from-file=.dockerconfigjson=/home/dir/.docker/config.json   \
    --type=kubernetes.io/dockerconfigjson
```

- `harborsecret` 表示key名
- `/home/dir/.docker/config.json` 表示docker认证文件，**注意要写绝对路径。**

#### 查看secret

```shell
$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
harborsecret          kubernetes.io/dockerconfigjson        1      7m5s
```

#### 查看内容

```shell
$ kubectl get secrets harborsecret --output="jsonpath={.data.\.dockerconfigjson}" | base64 -d
```

#### 查看yaml格式

```shell
$ kubectl get secret harborsecret --output=yaml
```

#### 删除secret

```shell
$ kubectl delete secrets harborsecret
```

#### 使用secret

通过 `imagePullSecrets` 来引用刚创建的 `harborsecret`

```yml
        spec:
            imagePullSecrets: 
              - name: harborsecret
            containers:
              - name: XXX
                image: XXX
```

