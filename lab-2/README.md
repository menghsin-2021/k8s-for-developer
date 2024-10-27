# Deployment, Daemonset, StatefulSets

## Deployment

1. 建立 deployment `nginx-deployment`.

```
$ kubectl apply -f ./nginx-deployment.yaml
deployment.apps/nginx-deployment created
```

2. 更新 `spec.replicas` 欄位 3 至 10 並 `kubectl apply` 更新。
3. 觀察 Deployment 狀態

```
$ kubectl rollout status deployment nginx-deployment


## 這些欄位代表？
$ kubectl describe deploy nginx-deployment
Name:                   nginx-deployment
Namespace:              default
CreationTimestamp:      Sun, 27 Oct 2024 14:24:25 +0800
Labels:                 app=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx
Replicas:               3 desired | 3 updated | 3 total | 0 available | 3 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
...
```

4. 更新 Nginx image 至 `1.26.2`。
5. 再次觀察 Deployment 狀態

```
$ kubectl rollout history deployment nginx-deployment
```

## StatefulSets

### prerequisites

> [!WARNING]
> 需要先設定好 Local Persistent Volume StorageClass

- 重複 Deplyment 步驟，觀察 rollout 行為是不是真的照著數字排序
<!-- TODO:  StatefulSets, PVC/PV issue 需要修正 -->

## DaemonSet

- TBD
<!-- TODO:  DaemonSet 要用 k8s 文件上 fluentd 嗎？ -->

## References

- https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
- https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
- https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/