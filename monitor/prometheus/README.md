# Prometheus

## Install Prometheus

### Docker
```sh
$ docker pull prom/prometheus
```

## How to collect MinIO's metric data

### Run Prometheus Server
```sh
$ docker run -d -p 9090:9090 \
  -v /path/to/prometheus.yml /etc/prometheus/prometheus.yml
  prom/prometheus
```

### Config target data - MinIO

MinIO exports prometheus metric data by default as an endpoint at `/minio/prometheus/metrics`.
User need to edit `prometheus.yaml` configuration to retrieve scrape data from this endpoint.

```yaml
# minio-prometheus.yaml
# Authenticated Prometheus config
scrape_configs:
- job_name: minio-job
  bearer_token: <secret>
  metrics_path: /minio/prometheus/metrics
  scheme: http
  static_configs:
  - targets: ['<minio_endpoint>:9000']

# Public Prometheus config
scrape_configs:
- job_name: minio-job
  metrics_path: /minio/prometheus/metrics
  scheme: http
  static_configs:
  - targets: ['<minio_endpoint>:9000']
```

## Prometheus Exporter

### K8S Nvidia GPU Exporter
`DaemonSet` with `dcgm-exporter.yaml` make nodes to expose the 9400 port as an hostport so that the `Prometheus` can fetch the metric data from exposed host port.
```yaml
# dcgm-exporter.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: "dcgm-exporter"
  labels:
    app.kubernetes.io/name: "dcgm-exporter"
    app.kubernetes.io/version: "2.0.0-rc.11"
spec:
  updateStrategy:
    type: RollingUpdate
  selector:
    matchLabels:
      app.kubernetes.io/name: "dcgm-exporter"
      app.kubernetes.io/version: "2.0.0-rc.11"
  template:
    metadata:
      labels:
        app.kubernetes.io/name: "dcgm-exporter"
        app.kubernetes.io/version: "2.0.0-rc.11"
      name: "dcgm-exporter"
    spec:
      containers:
        - image: "nvidia/dcgm-exporter:1.7.2"
          env:
            - name: "DCGM_EXPORTER_LISTEN"
              value: "9400"
            - name: "DCGM_EXPORTER_KUBERNETES"
              value: "true"
          name: "dcgm-exporter"
          ports:
            - name: "metrics"
              containerPort: 9400
              hostPort: 9400
          securityContext:
            runAsNonRoot: false
            runAsUser: 0
          volumeMounts:
            - name: "pod-gpu-resources"
              readOnly: true
              mountPath: "/var/lib/kubelet/pod-resources"
      volumes:
        - name: "pod-gpu-resources"
          hostPath:
            path: "/var/lib/kubelet/pod-resources"
```

User can deploy `POD`s managed by `DaemonSet` on each nodes with following commands.
```sh
$ kubectl apply -f dcgm-exporter.yaml
```

### Config target data - GPU Exporter
`Prometheus` can fetch the metric data from `POD` on each nodes.
```yaml
...
# gpu-prometheus.yaml
scrape_configs:
  - job_name: nvidia-dl-monitor
    scheme: http
    metrics_path: /metrics
    static_configs:
      - targets:
          - <K8S_WORKER_IP_1>:9400
          - <K8S_WORKER_IP_2>:9400
```

## Monitoring

### Grafana 
User can visualize the collected data with `Grafana`
```sh
$ docker pull grafana/grafana
```

### Prometheus UI
User also can visualize the collected data with `Prometheus UI` which is accessible at `localhost:9090` through web browsers