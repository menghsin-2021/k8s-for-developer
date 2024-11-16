# Pod

- 於 Kubernetes 執行的最小單位

## Lab

### （一）嘗試部署 Pod

1. 部署 Nginx Pod。

```bash
$ kubectl apply -f ./my-first-pod.yaml
pod/my-first-pod created
```

2. 觀察 Pod 有哪些資訊

```bash
$ kubectl get pod my-first-pod
NAME    READY   STATUS    RESTARTS   AGE
my-first-pod   1/1     Running   0          16m

$ kubectl get pod my-first-pod -o yaml

$ kubectl describe pod my-first-pod
```

1. 使用文字編輯器（nano or vim）增加 `spec.nodeName` 欄位指定 node。

```yaml
$ cat my-first-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeName: ip-10-66-148-166.ap-southeast-1.compute.internal --> 增加這一行，請使用 kubectl get node 取得目前 cluster 有的 node 名稱。
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```

4. `kubectl apply` 遇到這個錯誤是代表什麼意思？

```bash
$ kubectl apply -f my-first-pod.yaml
The Pod "my-first-pod" is invalid: spec: Forbidden: pod updates may not change fields other than `spec.containers[*].image`,`spec.initContainers[*].image`,`spec.activeDeadlineSeconds`,`spec.tolerations` (only additions to existing tolerations),`spec.terminationGracePeriodSeconds` (allow it to be set to 1 if it was previously negative)
  core.PodSpec{
  	... // 9 identical fields
  	ServiceAccountName:           "default",
  	AutomountServiceAccountToken: nil,
  	NodeName: strings.Join({
  		"ip-10-66-1",
- 		"9-182",
+ 		"48-166",
  		".ap-southeast-1.compute.internal",
  	}, ""),
  	SecurityContext:  &{},
  	ImagePullSecrets: nil,
  	... // 19 identical fields
  }
```

5. 刪除 Pod `nginx`。

```bash
$ kubectl delete pod my-first-pod --grace-period 0
pod "my-first-pod " deleted
```

6. 驗證 Pod 是否部署

```bash
$ kubectl apply -f ./my-first-pod.yaml
pod/my-first-pod created

$ kubectl get pod my-first-pod -o wide
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
  - name: ubuntu-container --> 請完成以下 ... 部分
    ...
    ...
    ...
```

## References

1. https://kubernetes.io/docs/concepts/workloads/pods/#using-pods
