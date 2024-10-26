# Pod

- 於 Kubernetes 執行的最小單位

## Lab

### （一）嘗試一下 Pod

1. 部署 Nginx Pod。

```
$ kubectl apply -f ./simple-pod.yaml
pod/nginx created
```

2. 來看一下 Pod 有哪些資訊

```
$ kubectl get po nginx
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          16m

$ kubectl get po nginx -o yaml

$ kubectl describe po nginx
```

3. 增加一下 `spec.nodeName` 指定 node。

```
$ cat simple-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: ip-10-66-148-166.ap-southeast-1.compute.internal
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

4. `kubectl apply` 遇到這個錯誤是代表什麼意思？

```
$ kubectl apply -f ./simple-pod.yaml
The Pod "nginx" is invalid: spec: Forbidden: pod updates may not change fields other than `spec.containers[*].image`,`spec.initContainers[*].image`,`spec.activeDeadlineSeconds`,`spec.tolerations` (only additions to existing tolerations),`spec.terminationGracePeriodSeconds` (allow it to be set to 1 if it was previously negative)
  core.PodSpec{
  	... // 9 identical fields
  	ServiceAccountName:           "default",
  	AutomountServiceAccountToken: nil,
  	NodeName: strings.Join({
  		"ip-10-66-1",
- 		"22-24",
+ 		"48-166",
  		".ap-southeast-1.compute.internal",
  	}, ""),
  	SecurityContext:  &{},
  	ImagePullSecrets: nil,
  	... // 19 identical fields
  }
```

5. 刪除 Pod `nginx`。

```
$ kubectl delete po nginx --grace-period 0
pod "nginx" deleted
```

6. 驗證 Pod 是否部署

```
$ kubectl apply -f ./simple-pod.yaml
pod/nginx created

$ kubectl get po nginx -o wide
NAME    READY   STATUS      RESTARTS   AGE   IP       NODE                                               NOMINATED NODE   READINESS GATES
nginx   0/1     Running   0          17s   <none>   ip-10-66-148-166.ap-southeast-1.compute.internal   <none>           <none>
```

### （二）增加一個 container

* 可以用 docker compose 類比

## 參考

1. https://kubernetes.io/docs/concepts/workloads/pods/#using-pods