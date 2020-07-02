## kubernetes helm 相关文档

### 基础命令

- 查找 helm search CHARTNAME
- 检查 helm inspect CHARTNAME
- 安装 helm install --name mem1 stable/memcached
- 获取状态信息 helm status mem1
- 列出 helm list [-a]
- 删除 helm delete [--purge] mem1
- 下载 helm fetch stable/redis
- 创建 helm create CHARTNAME
- 语法检测 helm lint CHARTNAME
- 打包 helm package CHARTNAME
- 显示状态 helm status NAME

### Chart 目录结构

```bash
mychart/
  Chart.yaml          # Yaml文件，用于描述Chart的基本信息，包括名称版本等
  LICENSE             # [可选] 协议
  README.md           # [可选] 当前Chart的介绍
  values.yaml         # Chart的默认配置文件
  requirements.yaml   # [可选] 用于存放当前Chart依赖的其它Chart的说明文件
  charts/             # [可选]: 该目录中放置当前Chart依赖的其它Chart
  templates/          # [可选]: 部署文件模版目录，模版使用的值来自values.yaml和由Tiller提供的值
  templates/NOTES.txt # [可选]: 放置Chart的使用指南
```

#### Chart.yaml 文件

```bash
name: [必须] Chart的名称
version: [必须] Chart的版本号，版本号必须符合 SemVer 2：http://semver.org/
description: [可选] Chart的简要描述
keywords:
  -  [可选] 关键字列表,便于检索
home: [可选] 项目地址
sources:
  - [可选] 当前Chart的下载地址列表
maintainers: # [可选]
  - name: [必须] 名字
    email: [可选] 邮箱
engine: gotpl # [可选] 模版引擎，默认值是gotpl
icon: [可选] 一个SVG或PNG格式的图片地址
```

#### requirements.yaml 文件

requirements.yaml 文件内容：

```bash
dependencies:
  - name: example
    version: 1.2.3
    repository: http://example.com/charts
  - name: Chart名称
    version: Chart版本
    repository: 该Chart所在的仓库地址
```

#### templates 目录

templates目录中存放了Kubernetes部署文件的模版。 例如：

```yml
# db.yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: deis-database
  namespace: deis
  labels:
    heritage: deis
spec:
  replicas: 1
  selector:
    app: deis-database
  template:
    metadata:
      labels:
        app: deis-database
    spec:
      serviceAccount: deis-database
      containers:
        - name: deis-database
          image: { {.Values.imageRegistry} }/postgres:{ {.Values.dockerTag} }
          imagePullPolicy: { {.Values.pullPolicy} }
          ports:
            - containerPort: 5432
          env:
            - name: DATABASE_STORAGE
              value: { {default "minio" .Values.storage} }
```

模版语法扩展了 golang/text/template的语法：

```json
# 这种方式定义的模版，会去除test模版尾部所有的空行
{ {- define "test"} }
模版内容
{ {- end} }
 
# 去除test模版头部的第一个空行
{ {- template "test" } }
```

用于yaml文件前置空格的语法：

```json
# 这种方式定义的模版，会去除test模版头部和尾部所有的空行
{ {- define "test" -} }
模版内容
{ {- end -} }
 
# 可以在test模版每一行的头部增加4个空格，用于yaml文件的对齐
{ { include "test" | indent 4} }
```

### 创建自己的chart

我们创建一个名为 mongodb 的 chart，看一看 chart 的文件结构

```bash
$ helm create mongodb
$ tree mongodb
mongodb
├── Chart.yaml #Chart本身的版本和配置信息
├── charts #依赖的chart
├── templates #配置模板目录
│   ├── NOTES.txt #helm提示信息
│   ├── _helpers.tpl #用于修改kubernetes objcet配置的模板
│   ├── deployment.yaml #kubernetes Deployment object
│   └── service.yaml #kubernetes Serivce
└── values.yaml #kubernetes object configuration
 
2 directories, 6 files
```

#### 模板

Templates目录下是yaml文件的模板，遵循[Go template](https://golang.org/pkg/text/template/)语法。使用过[Hugo](https://gohugo.io/)的静态网站生成工具的人应该对此很熟悉。

参考 [deployment.yaml](deployment.yaml) 文件的内容。

这是该应用的Deployment的yaml配置文件，其中的双大括号包扩起来的部分是Go template，其中的Values是在values.yaml文件中定义的：

```yml
# Default values for mychart.
# This is a yaml-formatted file.
# Declare variables to be passed into your templates.
replicaCount: 1
image:
  repository: nginx
  tag: stable
  pullPolicy: IfNotPresent
service:
  name: nginx
  type: ClusterIP
  externalPort: 80
  internalPort: 80
resources:
  limits:
    cpu: 100m
    memory: 128Mi
  requests:
    cpu: 100m
    memory: 128Mi
```

比如在Deployment.yaml中定义的容器镜像image: "{ { .Values.image.repository } }:{ { .Values.image.tag } }"其中的：

- .Values.image.repository就是nginx
- .Values.image.tag就是stable

以上两个变量值是在create chart的时候自动生成的默认值