# 在K8S集群中安装OpenTelemetry

## OpenTelemetry Operator 介绍

OpenTelemetry Operator是一个为了简化 OpenTelemetry 组件在 Kubernetes 环境中的部署和管理而设计的 Kubernetes Operator。

OpenTelemetry Operator 通过 CRD（OpenTelemetryCollector、Instrumentation、OpAMPBridge） 实现在 Kubernetes 集群中自动部署和管理 OpenTelemetry Collector；在工作负载中自动安装 OpenTelemetry 探针。

## 负载均衡器metallb部署

### 修改kube-proxy代理模式

```
# kubectl get configmap -n kube-system
NAME                                                   DATA   AGE
......
kube-proxy                                             2      35h
```

```
# kubectl edit configmap kube-proxy -n kube-system
   ipvs:
      excludeCIDRs: null
      minSyncPeriod: 0s
      scheduler: ""
      strictARP: true 由原来的flase修改为true
      syncPeriod: 0s
      tcpFinTimeout: 0s
      tcpTimeout: 0s
      udpTimeout: 0s
    kind: KubeProxyConfiguration
    logging:
      flushFrequency: 0
      options:
        json:
          infoBufferSize: "0"
      verbosity: 0
    metricsBindAddress: ""
    mode: "ipvs" 默认为空，添加ipvs
```

```
# kubectl rollout restart daemonset kube-proxy -n kube-system
```

### &#x20;metallb部署

#### &#x20;**metallb部署**

```
# kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.11/config/manifests/metallb-native.yaml
```

#### **8.2.2.2 IP地址池准备**

```
# vim ippool.yaml
# cat ippool.yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.10.240-192.168.10.250
```

```
# kubectl apply -f ippool.yaml
```

**8.2.2.3 开启二层通告**

```
# vim l2.yaml
# cat l2.yaml
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: example
  namespace: metallb-system
```

```
# kubectl apply -f l2.yaml
```

## 8.3 OpenTelemetry 后端 jaeger部署

为了便于演示这里使用 jaegertracing/all-in-one 镜像来部署 Jaeger，这个镜像包含了 Jaeger 收集器、内存存储、查询服务和 UI 等组件，非常适合开发和测试使用。

通过环境变量 COLLECTOR\_OTLP\_ENABLED 启动对 OTLP（OpenTelemetry Protocol）的支持。

```
# vim jaeger.yaml
# cat > jaeger.yaml << EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
      - name: jaeger
        image: jaegertracing/all-in-one:latest
        env:
        - name: COLLECTOR_OTLP_ENABLED
          value: "true"
        ports:
        - containerPort: 16686
        - containerPort: 14268
---
apiVersion: v1
kind: Service
metadata:
  name: jaeger
spec:
  selector:
    app: jaeger
  type: LoadBalancer
  ports:
    - name: ui
      port: 16686
      targetPort: 16686
    - name: collector
      port: 14268
      targetPort: 14268
    - name: http
      protocol: TCP
      port: 4318
      targetPort: 4318
    - name: grpc
      protocol: TCP
      port: 4317
      targetPort: 4317
EOF
```

```
# kubectl apply -f jaeger.yaml
deployment.apps/jaeger created
service/jaeger created
```

```
# kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                                                         AGE
jaeger       LoadBalancer   10.111.96.244   192.168.10.241   16686:32520/TCP,14268:31845/TCP,4318:30086/TCP,4317:32169/TCP   68s
```

## 8.4 cert-manager安装

Otel Operator 依赖 cert-manager 进行证书的管理，安装 operator 之前需要安装 cert-manager。

```
# kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.13.2/cert-manager.yaml
```

```
# # kubectl get ns
NAME                            STATUS   AGE
......
cert-manager                    Active   20s
```

```
# kubectl get pods -n cert-manager
NAME                                      READY   STATUS    RESTARTS   AGE
cert-manager-7d75f47cc5-mfdgl             1/1     Running   0          21s
cert-manager-cainjector-c778d44d8-ql2kq   1/1     Running   0          21s
cert-manager-webhook-55d76f97bb-k66gb     1/1     Running   0          21s
```

## 8.5 部署OpenTelemetry Operator

```
# wget https://github.com/open-telemetry/opentelemetry-operator/releases/latest/download/opentelemetry-operator.yaml
```

```
# kubectl apply -f opentelemetry-operator.yaml
```

```
# kubectl get ns
NAME                            STATUS   AGE
......
opentelemetry-operator-system   Active   32s
```

```
# kubectl get pods -n opentelemetry-operator-system
NAME                                                         READY   STATUS    RESTARTS   AGE
opentelemetry-operator-controller-manager-799f946479-x9qc9   2/2     Running   0          30s
```

```
# kubectl get svc -n opentelemetry-operator-system
NAME                                                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
opentelemetry-operator-controller-manager-metrics-service   ClusterIP   10.109.145.154   <none>        8443/TCP  33s
opentelemetry-operator-webhook-service                      ClusterIP   10.111.240.117   <none>        443/TCP    32s
```

## 8.6 部署OpenTelemetry Collector

通过创建 CR OpenTelemetry Collector 来配置 Otel 的采集器,Collector 部署的四种部署模型 Deployment、DaemonSet、StatefulSet、Sidecar，默认为 Deployment。

otel 接收器：支持 grpc（端口 4317）和 http（端口 4318） memory\_limiter 和 batch 处理器，但是为了方便快速查看数据，这两个并没有启用，仅作展示用。 debug 和 otlp/jaeger 的输出器，分别用于在标准输出中打印信息和使用 otlp 协议输出到 Jaeger。 pipeline 服务，用于配置跟踪数据的处理流程：接收、处理和输出。

```
# cat > opentelemetrycollector.yaml << EOF
apiVersion: opentelemetry.io/v1alpha1
kind: OpenTelemetryCollector
metadata:
  name: otel
spec:
  config: |
    receivers:
      otlp:
        protocols:
          grpc:
          http:
    processors:
      memory_limiter:
        check_interval: 1s
        limit_percentage: 75
        spike_limit_percentage: 15
      batch:
        send_batch_size: 10000
        timeout: 10s
​
    exporters:
      debug:
      otlp/jaeger:
        endpoint: "jaeger.default.svc.cluster.local.:4317"
        tls:
          insecure: true
​
    service:
      pipelines:
        traces:
          receivers: [otlp]
          processors: []
          exporters: [debug,otlp/jaeger]
EOF
```

```
# kubectl apply -f otc.yaml
opentelemetrycollector.opentelemetry.io/otel created
```

创建 CR OpenTelemetry Collector 后，Otel Operator 会创建一个 deployment 和 多个 service。

```
# kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
jaeger-66d4f7f6cf-dh5nr           1/1     Running   0          41m
otel-collector-5b74b97df5-bkrdj   1/1     Running   0          6s
```

```
# kubectl get deployment
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
otel-collector   1/1     1            1          15s
```

```
# kubectl get service
NAME                        TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                                                         AGE
​
......
otel-collector              ClusterIP      10.107.67.11    <none>           4317/TCP,4318/TCP                                               17s
otel-collector-headless     ClusterIP      None            <none>           4317/TCP,4318/TCP                                               17s
otel-collector-monitoring   ClusterIP      10.111.89.46    <none>           8888/TCP                                                        17s
```

## 8.7 配置 Instrumentation（插桩）

Instrumentation 是 Otel Operator 的另一个 CRD，用于自动安装 Otel 探针和配置：

* propagators 用于配置跟踪信息在上下文的传递方式。
* sampler 采样器
* env 和 \[language].env 添加到容器的环境变量

```
# cat > instrumentation.yaml << EOF
apiVersion: opentelemetry.io/v1alpha1
kind: Instrumentation
metadata:
  name: instrumentation-sample
spec:
  propagators:
    - tracecontext
    - baggage
    - b3
  sampler:
    type: parentbased_traceidratio
    argument: "1"
  env:
    - name: OTEL_EXPORTER_OTLP_ENDPOINT
      value: otel-collector.default.svc.cluster.local.:4318
  java:
    env:
      - name: OTEL_EXPORTER_OTLP_ENDPOINT
        value: http://otel-collector.default.svc.cluster.local.:4317
EOF
```

```
# kubectl apply -f instrumentation.yaml
instrumentation.opentelemetry.io/instrumentation-sample created
```

```
# kubectl get instrumentation
NAME                     AGE   ENDPOINT   SAMPLER                    SAMPLER ARG
instrumentation-sample   28s              parentbased_traceidratio   1
```

```
# kubectl describe instrumentation instrumentation-sample
Name:         instrumentation-sample
Namespace:    default
Labels:       app.kubernetes.io/managed-by=opentelemetry-operator
Annotations:  instrumentation.opentelemetry.io/default-auto-instrumentation-apache-httpd-image:
                ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-apache-httpd:1.0.3
              instrumentation.opentelemetry.io/default-auto-instrumentation-dotnet-image:
                ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-dotnet:1.2.0
              instrumentation.opentelemetry.io/default-auto-instrumentation-go-image:
                ghcr.io/open-telemetry/opentelemetry-go-instrumentation/autoinstrumentation-go:v0.8.0-alpha
              instrumentation.opentelemetry.io/default-auto-instrumentation-java-image:
                ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-java:1.32.0
              instrumentation.opentelemetry.io/default-auto-instrumentation-nginx-image:
                ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-apache-httpd:1.0.3
              instrumentation.opentelemetry.io/default-auto-instrumentation-nodejs-image:
                ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-nodejs:0.44.0
              instrumentation.opentelemetry.io/default-auto-instrumentation-python-image:
                ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-python:0.41b0
API Version:  opentelemetry.io/v1alpha1
Kind:         Instrumentation
Metadata:
  Creation Timestamp:  2023-12-14T09:38:32Z
  Generation:          1
  Resource Version:    257993
  UID:                 84a6daa3-5f12-481c-acc1-14b9ff5694f6
Spec:
  Apache Httpd:
    Config Path:  /usr/local/apache2/conf
    Image:        ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-apache-httpd:1.0.3
    Resource Requirements:
      Limits:
        Cpu:     500m
        Memory:  128Mi
      Requests:
        Cpu:     1m
        Memory:  128Mi
    Version:     2.4
  Dotnet:
    Image:  ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-dotnet:1.2.0
    Resource Requirements:
      Limits:
        Cpu:     500m
        Memory:  128Mi
      Requests:
        Cpu:     50m
        Memory:  128Mi
  Env:
    Name:   OTEL_EXPORTER_OTLP_ENDPOINT
    Value:  otel-collector.default.svc.cluster.local.:4318
  Exporter:
  Go:
    Image:  ghcr.io/open-telemetry/opentelemetry-go-instrumentation/autoinstrumentation-go:v0.8.0-alpha
    Resource Requirements:
      Limits:
        Cpu:     500m
        Memory:  32Mi
      Requests:
        Cpu:     50m
        Memory:  32Mi
  Java:
    Env:
      Name:   OTEL_EXPORTER_OTLP_ENDPOINT
      Value:  http://otel-collector.defaulti.svc.cluster.local.:4317
    Image:    ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-java:1.32.0
    Resources:
      Limits:
        Cpu:     500m
        Memory:  64Mi
      Requests:
        Cpu:     50m
        Memory:  64Mi
  Nginx:
    Config File:  /etc/nginx/nginx.conf
    Image:        ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-apache-httpd:1.0.3
    Resource Requirements:
      Limits:
        Cpu:     500m
        Memory:  128Mi
      Requests:
        Cpu:     1m
        Memory:  128Mi
  Nodejs:
    Image:  ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-nodejs:0.44.0
    Resource Requirements:
      Limits:
        Cpu:     500m
        Memory:  128Mi
      Requests:
        Cpu:     50m
        Memory:  128Mi
  Propagators:
    tracecontext
    baggage
    b3
  Python:
    Image:  ghcr.io/open-telemetry/opentelemetry-operator/autoinstrumentation-python:0.41b0
    Resource Requirements:
      Limits:
        Cpu:     500m
        Memory:  32Mi
      Requests:
        Cpu:     50m
        Memory:  32Mi
  Resource:
  Sampler:
    Argument:  1
    Type:      parentbased_traceidratio
Events:        <none>
```

\
