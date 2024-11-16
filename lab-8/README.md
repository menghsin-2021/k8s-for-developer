# Helm

1. 將範例 Helm Chart repo 加入

```bash
helm repo add demo-chart https://focaaby.github.io/k8s-for-developer-helm-charts/
```

2. 安裝 grpc server

```bash
helm install -f ./value-server.yaml echo-server demo-chart/grpc
```

3. 安裝 grpc client

```bash
helm install -f ./value-client.yaml echo-client demo-chart/grpc
```
