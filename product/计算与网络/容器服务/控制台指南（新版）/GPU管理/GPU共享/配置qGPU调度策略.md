TKE qGPU 提供 节点 / GPU 卡 二层调度能力，可以让您在节点和 GPU 卡两个维度灵活配置不同调度策略，进一步提升资源分配效率。

![image-20220705164317157](/Users/xiaoxubeii_1/Library/Application Support/typora-user-images/image-20220705164317157.png)

## 配置策略

您可以通过修改 qgpu-scheduler 启动参数修改策略。

1. 查询已部署 qgpu-scheduler 负载

```
$ kubectl get deploy -nkube-system | grep qgpu
qgpu-scheduler       1/1     1            1           6h28m
```

2. 修改启动参数，修改 `--priority` 和 `--node-priority`，支持分别支持设置 binpack / spread 两种策略

```
$ kubect edit deploy -nkube-system qgpu-scheduler
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
  labels:
    app.kubernetes.io/managed-by: Helm
  name: qgpu-scheduler
  namespace: kube-system
  ...
spec:
  ...
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: qgpu-scheduler
    spec:
      containers:
      - args:
        - -priority
        - binpack
        - -node-priority
        - spread
        command:
        - qgpu-scheduler
        env:
        - name: PORT
          value: "12345"
        image: ccr.ccs.tencentyun.com/tkeimages/elastic-gpu-scheduler:v1.0.2
        imagePullPolicy: Always
        name: qgpu-scheduler
```

## 典型场景

| 业务场景 | 节点策略 | GPU卡策略 | 效果 |
| ------------ | ------------ | ------------ | ------------ |
| 1.共享业务和整卡业务共存，整卡业务占比高<br/>2.避免共享业务造成 GPU 资源碎片，保证整卡业务需求 | binpack | binpack | 1. 优先调度到同一节点<br/>2. 共享业务优先调度到节点的同一 GPU 卡<br/>3. 资源碎片少 |
| 1. 共享业务为主（算力 / 显存需求低于 50%）<br/>2. 对卡故障隔离要求高<br/>3. 集群层面减少资源碎片 | binpack | spread | 1. 优先调度到同一节点<br/>2. 共享业务分散到节点的不同GPU卡<br/>3. 集群层面资源碎片少 |
| 1. 共享业务为主（算力 / 显存需求低于50%）<br/>2. 对节点故障隔离要求高<br/>3.分散业务，减少故障和资源相互影响 | spread | binpack | 1. 分散调度到不同节点<br/>2. 优先调度到节点的同一 GPU 卡 |
| 1. 共享业务为主（算力 / 显存需求低于 50%）<br/>2. 对节点及卡故障隔离要求高<br/>3. 分散业务，减少故障和资源相互影响 | spread | spread | 1. 分散调度到不同节点<br/>2. 分散到节点的不同 GPU 卡 |