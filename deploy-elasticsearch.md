## k8s elasticsearch 配置

#### k8s 配置

- [deploy-elasticsearch-7.4.2.yml](docs/deploy-elasticsearch-7.5.0.yml)：配置 `elasticsearch:7.4.2`
- [deploy-elasticsearch-7.5.0.yml](docs/deploy-elasticsearch-7.5.0.yml)：配置 `elasticsearch:7.5.0`

#### 验证 es

```shell
$ k get svc
NAME            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                         AGE
elasticsearch   NodePort    10.111.234.182   <none>        9200:30103/TCP,9300:30104/TCP   3m41s
```

查看 elasticsearch 连接

```shell
$ curl 10.111.234.182:9200
{
  "name" : "es-2",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "VndvooKITOGcuXv9T8juWQ",
  "version" : {
    "number" : "7.4.2",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "2f90bbf7b93631e52bafb59b3b049cb44ec25e96",
    "build_date" : "2019-10-28T20:40:44.881551Z",
    "build_snapshot" : false,
    "lucene_version" : "8.2.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

查看节点状态：

```shell
$ curl 10.111.234.182:9200/_cat/nodes?v
ip          heap.percent ram.percent cpu load_1m load_5m load_15m node.role master name
10.64.2.62             9          15   0    0.20    0.16     0.17 dilm      -      es-2
10.64.0.164           10          64   4    1.54    2.09     2.27 dilm      -      es-1
10.64.2.61            15          15   0    0.20    0.16     0.17 dilm      *      es-0
```

#### 问题解决：

问题一：7.4.2 版本

> "stacktrace": ["org.elasticsearch.bootstrap.StartupException: java.lang.IllegalArgumentException: setting [cluster.initial_master_nodes] is not allowed when [discovery.type] is set to [single-node]",

解决：

```yml
		env:
          - name: discovery.type
            value: zen
```

问题二：集群发现：

```
  - name: "discovery.zen.ping.unicast.hosts"
    value: "elasticsearch-discovery"
```

建立了elasticsearch-discovery服务

#### 参考资料

elasticsearch:7.5.0

> https://www.deepnetwork.com/blog/2020/01/27/ELK-stack-filebeat-k8s-deployment.html

elasticsearch:7.3.2

https://staight.github.io/2019/09/16/%E5%9C%A8k8s%E4%B8%8A%E9%83%A8%E7%BD%B2elasticsearch/



