## k8s taint 相关文档

### 污点 ( Taint ) 的组成

使用 `kubectl taint` 命令可以给某个 Node 节点设置污点，Node 被设置上污点之后就和 Pod 之间存在了一种相
斥的关系，可以让 Node 拒绝 Pod 的调度执行，甚至将 Node 已经存在的 Pod 驱逐出去。

每个污点的组成如下：

```
key=value:effect
```

每个污点有一个 key 和 value 作为污点的标签，其中 value 可以为空，effect 描述污点的作用。

当前 taint
effect 支持如下三个选项：

- **NoSchedule** ：表示 k8s 将不会将 Pod 调度到具有该污点的 Node 上
- **PreferNoSchedule** ：表示 k8s 将尽量避免将 Pod 调度到具有该污点的 Node 上
- **NoExecute** ：表示 k8s 将不会将 Pod 调度到具有该污点的 Node 上，同时会将 Node 上已经存在的 Pod 驱
  逐出去

### 设置污点
```
kubectl taint nodes node1 key1=value1:NoSchedule
```

### 查看污点

节点说明中，查找 Taints 字段

```
kubectl describe pod pod-name
```

### 去除污点
```
kubectl taint nodes node1 key1:NoSchedule-
```

