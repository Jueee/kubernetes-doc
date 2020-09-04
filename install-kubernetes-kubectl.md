### 安装 kubernetes 客户端 kubectl

#### 安装

```shell
$ wget https://storage.googleapis.com/kubernetes-release/release/v1.18.2/bin/linux/amd64/kubectl
$ chmod +x kubectl
$ sudo mv kubectl /usr/bin/
```
#### 查看版本

```shell
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.2", GitCommit:"52c56ce7a8272c798dbc29846288d7cd9fbae032", GitTreeState:"clean", BuildDate:"2020-04-16T11:56:40Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"18", GitVersion:"v1.18.2", GitCommit:"52c56ce7a8272c798dbc29846288d7cd9fbae032", GitTreeState:"clean", BuildDate:"2020-04-16T11:48:36Z", GoVersion:"go1.13.9", Compiler:"gc", Platform:"linux/amd64"}
```

#### 设置别名

```shell
$ vi .profile
alias k='kubectl'
alias kg="kubectl get"
$ source .profile
```

### 授权用户

当使用其他用户时，报错：

```shell
$ kubectl get node
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

解决方案：

```shell
# cp -r /home/ddb/.kube/ /home/ant/
```

若报错：

```shell
$ kubectl get node
error: error loading config file "/home/ant/.kube/config": open /home/ant/.kube/config: permission denied
```

则需赋文件夹权限：

```shell
# chown -R ant /home/ant/.kube/
```

