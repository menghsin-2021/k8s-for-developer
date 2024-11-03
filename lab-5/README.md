# Cluster Autoscaler, HorizontalPodAutoscaler

## Cluster Autoscaler

> [!WARNING]
> 需要先安裝好 [Cluster Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler)

* Demo：觸發 Cluster Autoscaler 長機器

## HorizontalPodAutoscaler

> [!WARNING]
> 需要先安裝好 [Metrics Server](https://github.com/kubernetes-sigs/metrics-server#readme)

1. 建立 PHP Apache Server

```bash
$ kubectl apply -f ./php-apache.yaml
deployment.apps/php-apache created
service/php-apache created
```

2. 建立 HorizontalPodAutoscaler

```bash
$ kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
horizontalpodautoscaler.autoscaling/php-apache autoscaled
```

3. 檢視 horizontalpodautoscaler 資訊

```bash
$ kubectl get horizontalpodautoscaler php-apache -o yaml

apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  creationTimestamp: "2024-11-02T17:32:28Z"
  name: php-apache
  namespace: default
  resourceVersion: "705131242"
  uid: 91c2b943-89df-41c7-b7a4-4f9fc07fabea
spec:
  maxReplicas: 10
  metrics:
  - resource:
      name: cpu
      target:
        averageUtilization: 50
        type: Utilization
    type: Resource
  minReplicas: 1
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: php-apache
status:
  currentMetrics: null
  desiredReplicas: 0
```

4. 製造一些流量觸發 CPU 使用率高

```bash
$ kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
```

5. 開啟另一個 terminal 分頁監控 horizontalpodautoscaler 狀態。

```bash
$ kubectl get hpa php-apache --watch
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         10        1          70s
php-apache   Deployment/php-apache   160%/50%   1         10        1          105s
php-apache   Deployment/php-apache   250%/50%   1         10        4          2m
php-apache   Deployment/php-apache   158%/50%   1         10        5          2m15s
```

6. 停止步驟四 contrl+C 流量

7. kubectl describe 檢視一下

```bash
$ kubectl describe hpa php-apache
Name:                                                  php-apache
Namespace:                                             default
Labels:                                                <none>
Annotations:                                           <none>
CreationTimestamp:                                     Sun, 03 Nov 2024 01:32:28 +0800
Reference:                                             Deployment/php-apache
Metrics:                                               ( current / target )
  resource cpu on pods  (as a percentage of request):  0% (1m) / 50%
Min replicas:                                          1
Max replicas:                                          10
Deployment pods:                                       3 current / 1 desired
Conditions:
  Type            Status  Reason            Message
  ----            ------  ------            -------
  AbleToScale     True    SucceededRescale  the HPA controller was able to update the target scale to 1
  ScalingActive   True    ValidMetricFound  the HPA was able to successfully calculate a replica count from cpu resource utilization (percentage of request)
  ScalingLimited  True    TooFewReplicas    the desired replica count is less than the minimum replica count
Events:
  Type     Reason                        Age    From                       Message
  ----     ------                        ----   ----                       -------
  Warning  FailedGetResourceMetric       7m40s  horizontal-pod-autoscaler  failed to get cpu utilization: unable to get metrics for resource cpu: no metrics returned from resource metrics API
  Warning  FailedComputeMetricsReplicas  7m40s  horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu resource metric value: failed to get cpu utilization: unable to get metrics for resource cpu: no metrics returned from resource metrics API
  Warning  FailedGetResourceMetric       7m25s  horizontal-pod-autoscaler  failed to get cpu utilization: did not receive metrics for targeted pods (pods might be unready)
  Warning  FailedComputeMetricsReplicas  7m25s  horizontal-pod-autoscaler  invalid metrics (1 invalid out of 1), first error is: failed to get cpu resource metric value: failed to get cpu utilization: did not receive metrics for targeted pods (pods might be unready)
  Normal   SuccessfulRescale             6m10s  horizontal-pod-autoscaler  New size: 4; reason: cpu resource utilization (percentage of request) above target
  Normal   SuccessfulRescale             5m55s  horizontal-pod-autoscaler  New size: 5; reason: cpu resource utilization (percentage of request) above target
  Normal   SuccessfulRescale             25s    horizontal-pod-autoscaler  New size: 3; reason: All metrics below target
  Normal   SuccessfulRescale             10s    horizontal-pod-autoscaler  New size: 1; reason: All metrics below target
```
