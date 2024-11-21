# 📌 SigNoz with Fluent Bit
Ref - https://signoz.io/docs/userguide/fluentbit_to_signoz/ \
\
📍 Fluent Bit is a super fast, lightweight, and highly scalable logging and metrics processor and forwarder, it serves a similar role to Promtail in the Loki ecosystem.

## Install SigNoz on Kubernetes
```
helm repo add signoz https://charts.signoz.io
helm repo list
kubectl create ns platform
helm pull signoz/signoz --untar
```
📍 At SigNoz we use opentelemetry collector to recieve logs which supports the fluentforward protocol. So you can forward your logs from your fluentBit agent to opentelemetry collector using fluentforward protocol.
\
\
Modify values.yaml \
要開給 SigNoz 的 Otel colloctor 一個新的 port 來監聽 fluentforward protocol, 這裡用 24224
```Yaml
# Default values for OtelCollector
otelCollector:
、、、
、、、
  ports:
      fluentforward:
        enabled: true
        containerPort: 24224
        servicePort: 24224
        nodePort: ""
        protocol: TCP
```
Add fluentforward reciever
```Yaml
# Default values for OtelCollector
otelCollector:
、、、
、、、
  # -- Configurations for OtelCollector
  # @default -- See `values.yaml` for defaults
  config:
    receivers:
      fluentforward:
        endpoint: 0.0.0.0:24224
```
Updating the logs pipeline which will collect logs from fluentforward and otlp receiver, processing it using batch processor and export it to clickhouse
```Yaml
# Default values for OtelCollector
otelCollector:
、、、
、、、
  # -- Configurations for OtelCollector
  # @default -- See `values.yaml` for defaults
  config:
    、、、
    、、、
    service:
      pipelines:
        logs:
          receivers: [otlp, httplogreceiver/heroku, httplogreceiver/json, fluentforward]
          processors: [batch]
          exporters: [clickhouselogsexporter]
```
Deploy SigNoz
```
helm --namespace platform install my-release signoz/signoz -f /home/ubuntu/signoz/values.yaml 
```
## Install Fluent Bit on Kubernetes

```
helm repo add fluent https://fluent.github.io/helm-charts
helm pull fluent/fluent-bit --untar
```

Modify values.yaml of Fluent Bit, output 改成：
\
(elasticsearch 沒用能註解掉)
```Yaml
[OUTPUT]
        Name forward
        Match kube.*
        Host ${ClusterIP of signoz-otel-collector}
        Port 24224
```
Deploy Fluent Bit
```
helm upgrade --install fluent-bit fluent/fluent-bit -f /home/ubuntu/fluent-bit/values.yaml -n platform
```

## (Optional) Fluent Bit as a sidecar container
For example, collect HDFS namenode logs
```YAML
、、、
、、、
spec:
  containers:
  - name: main-application
    、、、
    、、、
    volumeMounts:
      - name: beaver-logging-share-log
        mountPath: /opt/hadoop/logs
  - name: fluent-bit
    image: "cr.fluentbit.io/fluent/fluent-bit:3.1.9"
    imagePullPolicy: IfNotPresent
    command:
      - /fluent-bit/bin/fluent-bit
    args:
      - --workdir=/fluent-bit/etc
      - --config=/fluent-bit/etc/conf/fluent-bit.conf
    volumeMounts:
      - name: config
        mountPath: /fluent-bit/etc/conf
      - name: beaver-logging-share-log
        mountPath: /opt/hadoop/logs
  volumes:
    - name: beaver-logging-share-log
      emptyDir: {}
    - name: config
      configMap:
        name: fluent-bit
```

## (Optional) SigNoz uses external ClickHouse
