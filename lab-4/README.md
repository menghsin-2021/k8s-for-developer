# ConfigMap, Secret

## ConfigMap

1. 建立 ConfigMap `nginx-conf`。

```
$ kubectl apply -f ./nginx-cm.yaml
configmap/nginx-conf created
```

2. 更新 ConfigMap `nginx-conf` 設定檔 port 由 80 改成 8080 port 並透過 `kubectl apply` 更新 ConfigMap。

```
        listen       80;
        listen  [::]:80;
```

3. 檢視 Nginx 是否生效，為什麼？
	1. pod 要重新建立
	2. containerport 也需要改成 8080
## Secret

1. 使用 kubectl 命令建立 secret `backend-user`。

```bash
$ kubectl create secret generic backend-user --from-literal=backend-username='backend-admin'
```

2. Decode secret `backend-user`。
```
$ kubectl get secret/backend-user -o jsonpath='{.data.backend-username}' | base64 -d
```

3. 請參考 [Secrets | Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets-as-environment-variables) 文件關聯 secret `backend-user` 為環境變數 `$SECRET_USERNAME`。
4. 用 kubectl 進入至 Pod

```bash
$ kubectl exec -it nginx-deployment-5b55bc6455-g6tcm -- bash
```

5. 使用 `env` 印出環境變數 `$SECRET_USERNAME`，或使用 echo 印出環境變數 `$SECRET_USERNAME`

```bash
root@nginx-deployment-5b55bc6455-g6tcm:/# env | grep "SECRET_USERNAME"
SECRET_USERNAME=backend-admin

root@nginx-deployment-5b55bc6455-g6tcm:/# echo $SECRET_USERNAME
backend-admin
```

## Demo - external secrets operator

* 問題：環境變數到底誰該負責？DevOps/SRE 或是 developer？
