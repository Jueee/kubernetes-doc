### build MySQL

#### 目的

通过配置文件，在k8s上运行MySQL，并且在本地持久化

#### 配置文件

```yaml
apiVersion: v1
kind: Namespace
metadata:
    name: testnamespace
    labels:
        name: testnamespace
---
apiVersion: v1
kind: PersistentVolume
metadata:
    name: mysqldata
    namespace: testnamespace
    labels:
        app: mysqldata
spec:
    capacity:
        storage: 5Gi
    accessModes:
      - ReadWriteOnce
    hostPath:
        path: /home/dir/juetest/appdata/mysqldata
    persistentVolumeReclaimPolicy: Retain
---
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
    name: mysqldata
    namespace: testnamespace
spec:
    resources:
        requests:
            storage: 5Gi
    accessModes:
      - ReadWriteOnce
    selector:
        matchLabels:
            app: mysqldata
---
apiVersion: v1
kind: Service
metadata:
  name: juetest-mysql
  namespace: testnamespace
  labels:
    app: juetest-mysql
spec:
  type: NodePort
  ports:
    - name: juetest-mysql
      port: 3306
      nodePort: 30379
      targetPort: 3306
  selector:
    app: juetest-mysql
---
apiVersion: apps/v1
kind: Deployment
metadata:
    name: juetest-mysql
    namespace: testnamespace
spec:
    replicas: 1
    selector:
        matchLabels:
            app: juetest-mysql
    template:
        metadata:
            labels:
                app: juetest-mysql
        spec:
            imagePullSecrets: 
              - name: harborsecret
            containers:
              - name: juetest-mysql
                image: XXXX/mysql:v1.0.1
                imagePullPolicy: IfNotPresent
                ports:
                  - containerPort: 3306
                volumeMounts:
                  - name: mysqldata
                    mountPath: /var/lib/mysql
            volumes:
              - name: mysqldata
                persistentVolumeClaim:
                    claimName: mysqldata
```

#### 运行

```shell
$ k apply -f mysql-deployment.yml
```

#### 测试

容器内部用到的端口

```shell
$ k exec -it juetest-mysql-84d79bb8c6-2ktzg -n testnamespace -- bash
root@phishing-mysql-84d79bb8c6-2ktzg:/# mysql -hlocalhost -P3306 -uroot -p123456
```

容器外部访问容器内部端口

```shell
$ mysql -h172.16.245.196 -P3306 -uroot -p123456
```

容器所在主机需要监听的端口

```shell
$ mysql -h127.0.0.1 -P30379 -uroot -p123456
```

### 问题处理

#### initialize specified but the data directory

部署后日志报错：

```shell
$ k logs mysql-66946b4c48-7dngn
Initializing database
2020-09-01T10:34:38.008752-00:00 0 [ERROR] --initialize specified but the data directory has files in it. Aborting.
2020-09-01T10:34:38.008819-00:00 0 [ERROR] Aborting
```

参考资料：

- https://github.com/docker-library/mysql/issues/186
- https://dev.mysql.com/doc/refman/5.7/en/server-options.html#option_mysqld_ignore-db-dir

原因：

一个新的ext4磁盘分区通常不为空。有一个`lost+found`目录，已知mysql会阻塞该目录。您可以尝试添加`--ignore-db-dir=lost+found`到，`CMD`以确保确定。

解决方案：

1. 增加启动参数：

   ```
   name: mysql-master
   image: mysql:5.7
   args:
     - "--ignore-db-dir=lost+found"
   ```

2. 增加子目录

   ```
   	volumeMounts:
   	  - name: mysqldata
   		subPath: mysql
   		mountPath: /var/lib/mysql
   ```

