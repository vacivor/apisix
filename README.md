# APISIX Kubernetes Deployment

This repository contains the installation steps and Kubernetes YAML configurations for deploying Apache APISIX in a Kubernetes cluster.

## Prerequisites

- Kubernetes cluster (v1.20+ recommended)
- Helm 3 installed
- kubectl configured to access the cluster

Check versions:

```bash
kubectl version --client
helm version
```

## Add APISIX Helm Repository

```bash
helm repo add apisix https://charts.apiseven.com
helm repo update
```

## Install APISIX

没有配置存储插件，需要关闭持久化

```bash
helm install apisix apisix/apisix \
  --namespace ingress-apisix \                     # 部署到 ingress-apisix namespace
  --create-namespace \                             # 如果 namespace 不存在则自动创建
  --set ingress-controller.enabled=true \          # 启用 APISIX Ingress Controller
  --set ingress-controller.config.apisix.serviceNamespace=ingress-apisix \  # 指定 APISIX Gateway 所在 namespace
  --set etcd.replicaCount=1 \                      # etcd 副本数设置为 1（适用于开发环境）
  --set etcd.persistence.enabled=false \           # 关闭 etcd 持久化存储
  --set service.type=NodePort \                    # 通过 NodePort 暴露 APISIX Gateway
  --set ingress-controller.config.kubernetes.enableGatewayAPI=false \  # 禁用 Gateway API，仅使用 Kubernetes Ingress
  --set ingress-controller.gatewayProxy.createDefault=true              # 创建默认 gateway proxy service
```

## Verify Installation
```bash
kubectl get pods -n ingress-apisix
```
## Apply Example Route
```bash
kubectl apply -f yaml/apisix-route.yaml
```
