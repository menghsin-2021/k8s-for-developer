# Service, Ingress

## Service

1. 建立 Service `nginx-ervice`。

```
$ kubectl apply -f ./lab-3/nginx-svc.yaml
service/nginx-service created
```

2. 觀察 Service `nginx-ervice`。

```
## What is Endpoints
$ kubectl describe svc nginx-service

$ kubectl get svc nginx-service -o wide
```

3. 登入 Node 觀察，Service `ClusterIP` 行為

```
$ aws ssm start-session --target i-0b22a22eec53b9321

sh-4.2$ sudo iptables-save | grep "172.20.237.151"
-A KUBE-SERVICES -d 172.20.237.151/32 -p tcp -m comment --comment "default/nginx-service:name-of-service-port cluster IP" -m tcp --dport 80 -j KUBE-SVC-IQGXNJVVP26VHMIN
sh-4.2$
sh-4.2$ sudo iptables-save | grep "172.20.237.151"
-A KUBE-SERVICES -d 172.20.237.151/32 -p tcp -m comment --comment "default/nginx-service:name-of-service-port cluster IP" -m tcp --dport 80 -j KUBE-SVC-IQGXNJVVP26VHMIN

sh-4.2$ sudo iptables-save | grep "KUBE-SVC-IQGXNJVVP26VHMIN"
:KUBE-SVC-IQGXNJVVP26VHMIN - [0:0]
-A KUBE-SERVICES -d 172.20.237.151/32 -p tcp -m comment --comment "default/nginx-service:name-of-service-port cluster IP" -m tcp --dport 80 -j KUBE-SVC-IQGXNJVVP26VHMIN
-A KUBE-SVC-IQGXNJVVP26VHMIN -m comment --comment "default/nginx-service:name-of-service-port -> 10.66.124.173:80" -m statistic --mode random --probability 0.33333333349 -j KUBE-SEP-CN7GZORMVFTLVXHY
-A KUBE-SVC-IQGXNJVVP26VHMIN -m comment --comment "default/nginx-service:name-of-service-port -> 10.66.
```

4. 增加 `spec.type` 為 NodePort，並 `kubectl apply -f` 更新。
5. 登入 Node 觀察，Service `ClusterIP` 行為

```
$ kubectl get svc nginx-service -o wide
NAME            TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE   SELECTOR
nginx-service   NodePort   172.20.152.220   <none>        80:31282/TCP   22m   app=nginx

sh-4.2$ curl localhost:31282
```

6. (option) `spec.type` 為 loadbalancer，會由 cloud provider 建立 LB


## Ingress

- Demo 用

> [!WARNING]
> 需先安裝 AWS Load Balancer controller
> Ingress NGINX Controller

- TBD
- Demo apply 一個 Ingress 對應回 AWS LB 大概會長怎樣
- Demo apply 一個 Ingress 對應回 Ingress NGINX Controller 發生什麼事
