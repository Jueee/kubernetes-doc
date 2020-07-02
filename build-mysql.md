## build MySQL

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

