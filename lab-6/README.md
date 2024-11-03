# Service Account

## 存取

1. 建立 Pod `netshoot`。

```bash
$ kubectl apply -f ./netshoot.yaml
pod/netshoot created
```

2. 進入 Pod `netshoot`。

```bash
$ kubectl exec -it netshoot -- bash
```

3. 設定以下 `APISERVER`, `SERVICEACCOUNT`, `NAMESPACE`, `TOKEN`, `CACERT` 五個環境變數。

```bash
# Point to the internal API server hostname
netshoot:~# APISERVER=https://kubernetes.default.svc

# Path to ServiceAccount token
netshoot:~# SERVICEACCOUNT=/var/run/secrets/kubernetes.io/serviceaccount

# Read this Pod's namespace
netshoot:~# NAMESPACE=$(cat ${SERVICEACCOUNT}/namespace)

# Read the ServiceAccount bearer token
netshoot:~# TOKEN=$(cat ${SERVICEACCOUNT}/token)

# Reference the internal certificate authority (CA)
netshoot:~# CACERT=${SERVICEACCOUNT}/ca.crt
```

4. 使用 curl 命令並帶有 Bearer token 存取 Kubernetes API server。

```bash
# Explore the API with TOKEN
netshoot:~# curl --cacert ${CACERT} --header "Authorization: Bearer ${TOKEN}" -X GET ${APISERVER}/api
{
  "kind": "APIVersions",
  "versions": [
    "v1"
  ],
  "serverAddressByClientCIDRs": [
    {
      "clientCIDR": "0.0.0.0/0",
      "serverAddress": "ip-172-16-41-114.ap-southeast-1.compute.internal:443"
    }
  ]
}
```

* 提問：這是 k8s 預設行為嗎？這樣安全嗎？
