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

## Monitoring

### Grafana 
User can visualize the collected data with `Grafana`
```sh
$ docker pull grafana/grafana
```

### Prometheus UI
User also can visualize the collected data with `Prometheus UI` which is accessible at `localhost:9090` through web browsers