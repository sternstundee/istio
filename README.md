# Bookinfo 完整配置说明

本项目是 Istio 官方 Bookinfo 示例的完整部署清单，包含所有 Service、Deployment、VirtualService、DestinationRule、Gateway 等配置。

## 🔁 流量进入流程

1. **用户请求**：用户通过浏览器访问 `http://<IngressGateway IP>/productpage`。
2. **Gateway 入口**：
    - 配置在 `bookinfo-gateway.yaml` 中。
    - 匹配 `istio: ingressgateway` 标签的网关 Pod（即 Istio 安装时默认的 ingressgateway 服务）。
    - 接收 80 端口上的 HTTP 请求，`hosts: "*"` 表示匹配所有域名。

3. **入口 VirtualService**：
    - 定义在 `virtualservice-gateway.yaml` 中。
    - 绑定到 `bookinfo-gateway`。
    - 将 `/productpage`、`/static` 等路径的流量路由到 `productpage` 服务（K8s Service）。

4. **productpage 服务内部调用**：
    - `productpage` 调用 `details` 和 `reviews`。
    - `reviews` 再调用 `ratings`（取决于版本）。

5. **reviews 灰度发布控制**：
    - 通过 `virtualservice-reviews.yaml` 配置不同版本的权重。
    - `destinationrule-reviews.yaml` 定义版本子集（v1、v2、v3）。

## 📁 文件列表

- `gateway.yaml`：定义 Gateway（入口）
- `virtualservice-gateway.yaml`：绑定 Gateway 的流量入口 VS
- `virtualservice-reviews.yaml`：内部微服务灰度发布 VS
- `destinationrule-reviews.yaml`：定义 reviews 的版本子集
- `productpage.yaml`、`details.yaml`、`reviews.yaml`、`ratings.yaml`：服务部署 + 服务发现配置

---

建议部署顺序：
1. 先部署 Deployment/Service（4 个微服务）
2. 再部署 DestinationRule 和 VirtualService
3. 最后部署 Gateway 和入口 VirtualService

部署命令示例：

```bash
kubectl apply -f .
```

---
