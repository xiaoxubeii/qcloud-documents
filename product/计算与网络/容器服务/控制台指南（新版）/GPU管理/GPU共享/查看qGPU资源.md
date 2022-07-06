您可以通过定制版本的 Arena 查看 TKE 集群内 GPU 和 qGPU 资源分配情况。

## 什么是 Arena

Arena 是 kubeflow 提供的命令行工具，可供数据科学家轻而易举地运行和监控机器学习训练作业，并便捷地检查结果。Arena 提供了 `top` 命令，用于检查 Kubernetes 集群内的可用 GPU 资源。

我们基于开源 Arena 项目添加了对 qGPU 的支持，现在您可以使用定制版本的 Arena 查看 TKE 集群内 qGPU 资源分配情况。

## 安装 Arena for qGPU

```
$ git checkout git@github.com:elastic-ai/arena.git && make
$ ./bin/arena top node -m qgpu
```

## 查看 qGPU 节点概览

```bash
$ ./bin/arena top node -m qgpu
NAME         IPADDRESS    ROLE    STATUS  GPUs(Allocated/Total)  GPU_MEMORY(Allocated/Total)
172.16.0.60  172.16.0.60  <none>  Ready   1.2/2                  17.0/28.0 GiB
-----------------------------------------------------------------------------------------------------------
Allocated/Total GPUs of nodes which own resource tke.cloud.tencent.com/qgpu-memory In Cluster:
1.2/2 (60.0%)
Allocated/Total GPU Memory of nodes which own resource tke.cloud.tencent.com/qgpu-memory In Cluster:
17.0/28.0 GiB(60.7%)
[root@VM-0-60-centos arena]# ./bin/arena top node -m qgpu
NAME         IPADDRESS    ROLE    STATUS  GPUs(Allocated/Total)  GPU_MEMORY(Allocated/Total)
172.16.0.60  172.16.0.60  <none>  Ready   1.2/2                  17.0/28.0 GiB
-----------------------------------------------------------------------------------------------------------
Allocated/Total GPUs of nodes which own resource tke.cloud.tencent.com/qgpu-memory In Cluster:
1.2/2 (60.0%)
Allocated/Total GPU Memory of nodes which own resource tke.cloud.tencent.com/qgpu-memory In Cluster:
17.0/28.0 GiB(60.7%)
```

## 查看 qGPU 节点详情

```
$ ./bin/arena top node -m qgpu -d

Name:    172.16.0.60
Status:  Ready
Role:    <none>
Type:    QGPU
Address: 172.16.0.60
Description:
  1.This node is enabled qgpu mode.
  2.Pods can request resource 'tke.cloud.tencent.com/qgpu-core / tke.cloud.tencent.com/qgpu-memory' to use qgpu feature on this node
Pods:
  NAMESPACE  NAME    GPU_MEM(Requested)  GPU_MEM(Allocated)
  ---------  ----    ------------------  ------------------
  default    test-1  1.0 GiB             gpu0(1.0GiB)
  default    test-2  2.0 GiB             gpu0(2.0GiB)
  default    test-3  14.0 GiB            gpu1(14.0GiB)
GPUs:
  INDEX  MEMORY(Total)  MEMORY(Allocated)  PERCENT
  -----  -------------  -----------------  -------
  0      14 GiB         3 GiB              21.4%
  1      14 GiB         14 GiB             100.0%
GPU Summary:
  Total GPUs: 2
  Allocated GPUs: 1.2
  Unhealthy GPUs: 0
  Total GPU Memory: 28.0 GiB
  Allocated GPU Memory: 17.0 GiB
```

