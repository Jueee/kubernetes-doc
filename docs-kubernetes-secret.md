## k8s secret 相关文档

### 基于现有Docker凭据

#### 创建secret

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

```yml
        spec:
            imagePullSecrets: 
              - name: harborsecret
            containers:
              - name: XXX
                image: XXX
```

