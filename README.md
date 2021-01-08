# prom-jmx

## 获取 adapter 指标

- 列出由prometheus提供的自定义指标: `kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1" | jq .`
- 查看新创建的api群组: `kubectl api-versions  | grep metrics`

  ```bash
  # kubectl api-versions  | grep metrics
  custom.metrics.k8s.io/v1beta1
  custom.metrics.k8s.io/v1beta2
  external.metrics.k8s.io/v1beta1
  metrics.k8s.io/v1beta1

  # kubectl api-versions  | grep autoscaling
  autoscaling/v1
  autoscaling/v2beta1
  autoscaling/v2beta2
  ```

- Pod 的Prometheus 适配器所提供的缺省定制指标: `kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1" | jq .  |grep "pods/"`

- 举例来说,需要对 java 应用 `jvm_threads_states_threads` 指标 HPA, 查询:

  ```bash
  # kubectl get --raw "/apis/custom.metrics.k8s.io/v1beta1/namespaces/demo-jmx/pods/*/jvm_threads_states_threads"
  {"kind":"MetricValueList","apiVersion":"custom.metrics.k8s.io/v1beta1","metadata":{"selfLink":"/apis/custom.metrics.k8s.io/v1beta1/namespaces/demo-jmx/pods/%2A/jvm_threads_states_threads"},"items":[{"describedObject":{"kind":"Pod","namespace":"demo-jmx","name":"demo-app-5d45f58f59-wz5fm","apiVersion":"/v1"},"metricName":"jvm_threads_states_threads","timestamp":"2021-01-07T10:19:41Z","value":"27","selector":null}]}
  ```

## 自定义指标 HPA

```bash
# kubectl describe hpa -n demo-jmx demo-app
Name:                                    demo-app
Namespace:                               demo-jmx
Labels:                                  app=demo-app
Annotations:                             CreationTimestamp:  Fri, 08 Jan 2021 15:04:59 +0800
Reference:                               Deployment/demo-app
Metrics:                                 ( current / target )
  "jvm_threads_states_threads" on pods:  27 / 10
Min replicas:                            2
Max replicas:                            10
Deployment pods:                         2 current / 4 desired
Conditions:
  Type            Status  Reason            Message
  ----            ------  ------            -------
  AbleToScale     True    SucceededRescale  the HPA controller was able to update the target scale to 4
  ScalingActive   True    ValidMetricFound  the HPA was able to successfully calculate a replica count from pods metric jvm_threads_states_threads
  ScalingLimited  True    ScaleUpLimit      the desired replica count is increasing faster than the maximum scale rate
Events:
  Type    Reason             Age   From                       Message
  ----    ------             ----  ----                       -------
  Normal  SuccessfulRescale  54s   horizontal-pod-autoscaler  New size: 2; reason: Current number of replicas below Spec.MinReplicas
  Normal  SuccessfulRescale  24s   horizontal-pod-autoscaler  New size: 4; reason: pods metric jvm_threads_states_threads above target
# kubectl get hpa -n demo-jmx
NAME       REFERENCE             TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
demo-app   Deployment/demo-app   27/10     2         10        4          98s
# kubectl get hpa -n demo-jmx
NAME       REFERENCE             TARGETS     MINPODS   MAXPODS   REPLICAS   AGE
demo-app   Deployment/demo-app   27100m/10   2         10        10         15m
```

TODO:

随便找了一个指标测试了 HPA, 指标突然变成 `27100m/10`, 疯狂扩容, 需要研究一下, 为什么之前是 27/10
