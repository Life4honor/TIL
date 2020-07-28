# Elastic Cloud on K8S

## Install CRDs & Operator
```sh
# Install custom resource definitions and the operator with its RBAC rules
$ kubectl apply -f https://download.elastic.co/downloads/eck/1.1.2/all-in-one.yaml
```

## Elasticsearch

### Deploy Elasticsearch on K8S cluster

```sh
$ kubectl apply -f elasticsearch.yaml
```

### Kubernetes manifest sample
```yaml
# elasticsearch.yaml
apiVersion: elasticsearch.k8s.elastic.co/v1
kind: Elasticsearch
metadata:
  name: elasticsearch
spec:
  http:
    tls: 
      selfSignedCertificate:
        disabled: true
  version: 7.8.0
  nodeSets:
  - name: default
    count: 1 # The number of elasticsearch nodes
    config:
      node.master: true
      node.data: true
      node.ingest: true
      node.store.allow_mmap: false # Set true for performance in production
    podTemplate:
      metadata:
        annotations:
        traffic.sidecar.istio.io/includeInboundPorts: "*"
        traffic.sidecar.istio.io/excludeOutboundPorts: "9300" 
        traffic.sidecar.istio.io/excludeInboundPorts: "9300"
      spec:
        automountServiceAccountToken: true 
        volumes:
        - name: elasticsearch-data
          emptyDir: {}
```
[PVC Configuration Guide](https://www.elastic.co/guide/en/cloud-on-k8s/current/k8s-volume-claim-templates.html)

## Kibana

### Deploy Kibana on K8S cluster
```sh
$ kubectl apply -f kibana.yaml
```

### Kubernetes manifest sample
```yaml
# kibana.yaml
apiVersion: kibana.k8s.elastic.co/v1
kind: Kibana
metadata:
  name: kibana
spec:
  version: 7.8.0
  count: 1
  elasticsearchRef:
    name: elasticsearch
  http:
    tls: 
      selfSignedCertificate:
        disabled: true
  podTemplate:
    spec:
      automountServiceAccountToken: true 
```

## APM Server

### Deploy APM Server on K8S cluster
```sh
$ kubectl apply -f apm-server.yaml
```
### Kubernetes manifest sample
```yaml
# apm-server.yaml
apiVersion: apm.k8s.elastic.co/v1
kind: ApmServer
metadata:
  name: elasticsearch
spec:
  version: 7.8.0
  count: 1
  elasticsearchRef:
    name: elasticsearch
  http:
    tls: 
      selfSignedCertificate:
        disabled: true
  podTemplate:
    metadata:
      annotations:
        sidecar.istio.io/rewriteAppHTTPProbers: "true" 
    spec:
      automountServiceAccountToken: true
```
## Metricbeat

### Deploy Metricbeat on K8S with DaemonSet
```sh
# Download the Kubernetes manifest for Metricbeat DaemonSet
$ curl -L -O https://raw.githubusercontent.com/elastic/beats/7.8/deploy/kubernetes/metricbeat-kubernetes.yaml

# Deploy Metricbeat DaemonSet on K8S cluster
$ kubectl apply -f metricbeat-kubernetes.yaml
```

```yaml
# ConfigMap for Metricbeat DaemonSet
apiVersion: v1
kind: ConfigMap
metadata:
  name: metricbeat-daemonset-config
  namespace: kube-system
  labels:
    k8s-app: metricbeat
data:
  metricbeat.yml: |-
    metricbeat.config.modules:
      ...

    output.elasticsearch:
      hosts: ['${ELASTICSEARCH_HOST:elasticsearch}:${ELASTICSEARCH_PORT:9200}']
      username: ${ELASTICSEARCH_USERNAME}
      password: ${ELASTICSEARCH_PASSWORD}
```

```yaml
# ConfigMap for modules
apiVersion: v1
kind: ConfigMap
metadata:
  name: metricbeat-daemonset-modules
  namespace: kube-system
  labels:
    k8s-app: metricbeat
data:
  system.yml: |-
    - module: system
      ...
  kubernetes.yml: |-
    - module: kubernetes
      ...
```

## Reference
[Elastic Cloud on Kubernetes](https://www.elastic.co/guide/en/cloud-on-k8s/1.1/index.html)

[Run Metricbeat on Kubernetes](https://www.elastic.co/guide/en/beats/metricbeat/current/running-on-kubernetes.html#running-on-kubernetes)
