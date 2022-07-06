
qGPU 是腾讯云推出的 GPU 共享技术，支持在多个容器间共享 GPU 卡并提供容器间显存、算力强隔离的能力，从而在更小粒度的使用 GPU 卡的基础上，保证业务安全，达到提高 GPU 使用率、降低客户成本的目的。
qGPU 依托 TKE 对外开源的 [Elastic GPU](https://github.com/elastic-ai/elastic-gpu) 框架，可实现对 GPU 算力与显存的细粒度调度，并支持多容器共享 GPU 与多容器跨 GPU 资源分配。同时依赖底层强大的 qGPU 隔离技术，可做到 GPU 显存和算力的强隔离，在通过共享使用 GPU 的同时，尽量保证业务性能与资源不受干扰。

## 产品优势

- **灵活性：**精细配置 GPU 算力占比和显存大小。
- **强隔离：**支持显存和算力的严格隔离。
- **在离线：**支持业界唯一在离线混部能力，GPU 利用率压榨到极致。
- **覆盖度：**支持主流架构 Volta（如 V100 等）、Turing（如 T4 等）、Ampere（如 A100、A10 等)。
- **云原生：**支持标准 Kubernetes 和 NVIDIA Docker。
- **兼容性：**业务不重编、CUDA 库不替换、业务无感。
- **高性能：**GPU 设备底层虚拟化，高效收敛，吞吐接近0损耗。

## 技术架构

![](https://qcloudimg.tencent-cloud.cn/raw/bff6e5677c81fb9117a284b880261c67.png)

