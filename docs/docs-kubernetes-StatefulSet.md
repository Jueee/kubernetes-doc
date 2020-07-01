### k8s StatefulSet 相关文档

#### 解决问题

Kafka和zookeeper是在两种典型的有状态的集群服务。首先kafka和zookeeper都需要存储盘来保存有状态信息，其次kafka和zookeeper每一个实例都需要有对应的实例Id (Kafka需要broker.id,zookeeper需要my.id)来作为集群内部每个成员的标识，集群内节点之间进行内部通信时需要用到这些标识。

对于这类服务的部署，需要解决两个大的问题，一个是状态保存，另一个是集群管理(多服务实例管理)。

kubernetes中提的 StatefulSet 方便了有状态集群服务在上的部署和管理。具体来说是通过 Init Container来做集群的初始化工作，用 Headless Service 来维持集群成员的稳定关系，用 Persistent Volume 和 Persistent Volume Claim提供网络存储来持久化数据，从而支持有状态集群服务的部署。

#### 关于 StatefulSet

- 匹配 Pod name ( 网络标识 ) 的模式为：`$(statefulset名称)-$(序号)`，比如：web-0，web-1，
  web-2
- StatefulSet 为每个 Pod 副本创建了一个 DNS 域名，这个域名的格式为： `$(podname).(headless server`
  `name)`，也就意味着服务间是通过Pod域名来通信而非 Pod IP，因为当Pod所在Node发生故障时， Pod 会
  被飘移到其它 Node 上，Pod IP 会发生变化，但是 Pod 域名不会有变化
- StatefulSet 使用 Headless 服务来控制 Pod 的域名，这个域名的 FQDN 为：`$(service`
  `name).$(namespace).svc.cluster.local`，其中，“cluster.local” 指的是集群的域名。
- 根据 volumeClaimTemplates，为每个 Pod 创建一个 pvc，pvc 的命名规则匹配模式：
  `(volumeClaimTemplates.name)-(pod_name)`，比如 volumeMounts.name=www， Pod
  name=web-[0-2]，因此创建出来的 PVC 是 www-web-0、www-web-1、www-web-2
- 删除 Pod 不会删除其 pvc，手动删除 pvc 将自动释放 pv

#### Statefulset的启停顺序

- **有序部署：**

  部署StatefulSet时，如果有多个Pod副本，它们会被顺序地创建（从0到N-1）并且，在下一个
  Pod运行之前所有之前的Pod必须都是Running和Ready状态。

- **有序删除**：

  当Pod被删除时，它们被终止的顺序是从N-1到0。

- **有序扩展**：

  当对Pod执行扩展操作时，与部署一样，它前面的Pod必须都处于Running和Ready状态。

#### StatefulSet使用场景

- 稳定的持久化存储，即Pod重新调度后还是能访问到相同的持久化数据，基于 PVC 来实现。
- 稳定的网络标识符，即 Pod 重新调度后其 PodName 和 HostName 不变。
- 有序部署，有序扩展，基于 init containers 来实现。
- 有序收缩。