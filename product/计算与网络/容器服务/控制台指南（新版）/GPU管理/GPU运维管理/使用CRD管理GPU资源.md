## 背景

TKE 提供基于 qGPU 的算力 / 显存强隔离的 GPU 共享方案，单纯通过扩展资源较难看到 GPU 具体分配信息。现在您可以通过开启 CRD 方式查看 GPU 资源的详细信息。

## 开启 GPU CRD

您可以通过修改 qgpu 组件启动参数开启 CRD 能力。

1. 查询已部署 qgpu 组件

```
$ kubectl get deploy -nkube-system | grep qgpu
qgpu-scheduler       1/1     1            1           6h28m
$ kubectl get ds -nkube-system | grep qgpu
qgpu-manager        1         1         1       1            1           qgpu-device-enable=enable   6h52m
```

2. 修改 qgpu-scheduler 启动参数，添加 `-crd`

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
        - -crd
        command:
        - qgpu-scheduler
        env:
        - name: PORT
          value: "12345"
        image: ccr.ccs.tencentyun.com/tkeimages/elastic-gpu-scheduler:v1.0.2
        imagePullPolicy: Always
        name: qgpu-scheduler
```

3. 修改 qgpu-manager 启动参数，添加 `-crd`

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  ...
  name: qgpu-manager
  namespace: kube-system
  ...
spec:
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: qgpu-manager
  template:
    metadata:
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      creationTimestamp: null
      labels:
        app: qgpu-manager
    spec:
      containers:
      - command:
        - /usr/bin/qgpu-manager
        - --nodename=$(NODE_NAME)
        - --dbfile=/host/var/lib/qgpu/meta.db
        - -crd
        env:
        - name: KUBECONFIG
          value: /etc/kubernetes/kubelet.conf
        - name: NODE_NAME
          valueFrom:
            fieldRef:
              apiVersion: v1
              fieldPath: spec.nodeName
        image: ccr.ccs.tencentyun.com/tkeimages/elastic-gpu-agent:v1.0.3
        imagePullPolicy: Always
        name: qgpu-manager
```

## 查看 GPU 分配情况

1. 执行以下命令，查看集群 GPU 物理卡。

```
$ kubectl get gpus
NAME             AGE
172.16.0.60-00   16s
172.16.0.60-01   16s
```

2. 执行以下命令，查看 GPU 物理卡详情。

```
$ kubectl get gpus -oyaml 172.16.0.60-00
apiVersion: elasticgpu.io/v1alpha1
kind: GPU
metadata:
  creationTimestamp: "2022-07-05T09:54:16Z"
  generation: 1
  labels:
    elasticgpu.io/node: 172.16.0.60
  name: 172.16.0.60-00
  resourceVersion: "8434800546"
  selfLink: /apis/elasticgpu.io/v1alpha1/gpus/172.16.0.60-00
  uid: d1fa02ff-b101-4238-9d8c-7e48d6920b14
spec:
  index: 0
  memory: 15843721216
  model: Tesla T4
  nodeName: 172.16.0.60
  path: /dev/nvidia0
  uuid: GPU-7286305a-6812-6dbd-890f-aa83026232a6
status:
  capacity:
    tke.cloud.tencent.com/qgpu-core: "100"
    tke.cloud.tencent.com/qgpu-memory: "14"
```

