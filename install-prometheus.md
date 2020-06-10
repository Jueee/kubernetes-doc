## 安装 Prometheus

官网

> https://prometheus.io/

GitHub

> https://github.com/coreos/kube-prometheus

### 组件说明

- MetricServer：是kubernetes集群资源使用情况的聚合器，收集数据给kubernetes集群内使用，如
  kubectl,hpa,scheduler等。 
- PrometheusOperator：是一个系统监测和警报工具箱，用来存储监控数据。
- NodeExporter：用于各node的关键度量指标状态数据。 
- KubeStateMetrics：收集kubernetes集群内资源对象数
  据，制定告警规则。 
- Prometheus：采用pull方式收集apiserver，scheduler，controller-manager，kubelet组件数
  据，通过http协议传输。 
- Grafana：是可视化数据统计和监控平台。

### 安装

##### 下载

```
$ git clone https://github.com/coreos/kube-prometheus.git
$ cd kube-prometheus/manifests
```

修改 grafana-service.yaml 文件，使用 nodepode 方式访问 grafana：

```yml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: grafana
  name: grafana
  namespace: monitoring
spec:
  type: NodePort
  ports:
  - name: http
    port: 3000
    targetPort: http
    nodePort: 31100
  selector:
    app: grafana
```

修改 prometheus-service.yaml，改为 nodepode：

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    prometheus: k8s
  name: prometheus-k8s
  namespace: monitoring
spec:
  type: NodePort
  ports:
  - name: web
    port: 9090
    targetPort: web
    nodePort: 31200
  selector:
    app: prometheus
    prometheus: k8s
  sessionAffinity: ClientIP
```

修改 alertmanager-service.yaml，改为 nodepode

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    alertmanager: main
  name: alertmanager-main
  namespace: monitoring
spec:
  type: NodePort
  ports:
  - name: web
    port: 9093
    targetPort: web
    nodePort: 31300
  selector:
    alertmanager: main
    app: alertmanager
  sessionAffinity: ClientIP
```

创建名称空间和CRD

```
$ kubectl create -f manifests/setup
```

等待资源可用后，安装

```
$ kubectl create -f manifests/
```

### 访问 

##### 访问 Prometheus

> http://MasterIP:31200/graph

##### 访问 Grafana

> http://MasterIP:31100

##### 访问报警平台 AlertManager

> http://MasterIP:31300/

### Prometheus PromQL

Prometheus提供一个函数式的表达式语言PromQL (Prometheus Query Language)，可以使用户实时地查找和聚合时间序列数据。

获取集群级别的CPU使用率：

```
sum (rate (container_cpu_usage_seconds_total{id="/"}[1m])) / sum (machine_cpu_cores) * 100
```

每个Pod的CPU使用率：

```
sum (rate (container_cpu_usage_seconds_total{image!=""}[1m])) by (pod_name)
```

