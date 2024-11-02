# Pod

- 於 Kubernetes 執行的最小單位

## Lab

### （一）嘗試一下 Pod

1. 部署 Nginx Pod。

```bash
$ kubectl apply -f ./my-first-pod.yaml
pod/nginx created
```

2. 來看一下 Pod 有哪些資訊

```bash
$ kubectl get po nginx
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          16m

$ kubectl get po nginx -o yaml

$ kubectl describe po nginx
```

3. 增加一下 `spec.nodeName` 指定 node。

```yaml
$ cat my-first-pod.yaml
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

```bash
$ kubectl apply -f ./my-first-pod.yaml
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

```bash
$ kubectl delete po nginx --grace-period 0
pod "nginx" deleted
```

6. 驗證 Pod 是否部署

```bash
$ kubectl apply -f ./my-first-pod.yaml
pod/nginx created

$ kubectl get po nginx -o wide
NAME    READY   STATUS      RESTARTS   AGE   IP       NODE                                               NOMINATED NODE   READINESS GATES
nginx   0/1     Running   0          17s   <none>   ip-10-66-148-166.ap-southeast-1.compute.internal   <none>           <none>
```

### （二）增加一個 container

1. 檢視 [docker-compose.yaml](./docker-compose.yaml) 檔案。

```bash
$ cat ./docker-compose.yaml
services:
  nginx:
    image: "nginx:1.26.2"
    ports:
      - "8080:80"
  ubuntu:
    image: "ubuntu:24.04"
    entrypoint: "sleep"
    command: "3600"
```

2. 參考上述 [docker-compose.yaml](./docker-compose.yaml)，增加一個 ubuntu container 也是分成類似 entrypoint/command 寫法。

```bash
$ cat ./my-first-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-first-pod
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

## References

1. https://kubernetes.io/docs/concepts/workloads/pods/#using-pods
