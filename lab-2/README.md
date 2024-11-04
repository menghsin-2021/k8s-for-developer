# Deployment, Daemonset, StatefulSets

## Deployment

1. 建立 deployment `nginx-deployment`.

```bash
$ kubectl apply -f ./nginx-deployment.yaml
deployment.apps/nginx-deployment created
```

2. 更新 `spec.replicas` 欄位 3 至 10 並 `kubectl apply` 更新。
3. 觀察 deployment 狀態

```bash
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

```bash
$ kubectl set image deployment/nginx-deployment nginx=nginx:1.26
```

5. 再次觀察 deployment 狀態

```bash
$ kubectl rollout history deployment nginx-deployment
```

## StatefulSets

### prerequisites

> [!WARNING]
> 需要先設定好 Local Persistent Volume StorageClass

- 重複 Deplyment 步驟，觀察 rollout 行為是不是真的照著數字排序
<!-- TODO:  StatefulSets, PVC/PV issue 需要修正 -->

## DaemonSet

1. 建立 daemonset `nginx-daemonset`.

```bash
$ kubectl apply -f ./nginx-daemonset.yaml
daemonset.apps/nginx-daemonset created
```

2. 更新 Nginx image 至 `1.26.2`。

```bash
$ kubectl set image daemonset/nginx-daemonset nginx=nginx:1.26
```

3. 觀察 daemonset 狀態

```bash
$ kubectl get ds nginx-daemonset
NAME              DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
nginx-daemonset   11        11        6       5            6           <none>          67s

$ kubectl rollout status daemonset nginx-daemonset
Waiting for daemon set "nginx-daemonset" rollout to finish: 5 out of 11 new pods have been updated...
```

## References

- https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
- https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
- https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
