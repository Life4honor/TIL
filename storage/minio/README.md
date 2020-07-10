# MinIO

## Installation

### Docker

```sh
$ docker pull minio/minio
```

### Kubernetes

```sh
# Install MinIO-Operator with default configuration
$ kubectl apply -f https://raw.githubusercontent.com/minio/minio-operator/master/minio-operator.yaml
```

```sh
# User can leverage kustomize to customize operator configuration
$ git clone https://github.com/minio/minio-operator
$ cd operator-deployment
$ kustomize build | kubectl apply -f -
```

## Run - Docker

### Run Standalone MinIO

```sh
$ docker run -p 9000:9000 minio/minio server /data
```

### Run MinIO Server with Erasure Code

```sh
$ docker run -p 9000:9000 --name minio \
  -v /mnt/data1:/data1 \
  -v /mnt/data2:/data2 \
  -v /mnt/data3:/data3 \
  -v /mnt/data4:/data4 \
  minio/minio server /data{1...4}
```

## Run - Kubernetes
```sh
# User can create MinIO instances using the below command.
$ kubectl apply -f https://raw.githubusercontent.com/minio/minio-operator/master/examples/minioinstance.yaml
```

```sh
# POD initiate the container with default entrypoint below
$ minio server --certs-dir /tmp/certs http://minio-{0...3}.minio-hl-svc.default.svc.cluster.local/export{0...3}
```
User can configure the `mountPath`

```yaml
# minioinstance.yaml

serviceName: minio-service
zones:
  - name: "zone-0"
    ## Number of MinIO servers/pods in this zone.
    ## For standalone mode, supply 1. For distributed mode, supply 4 or more.
    ## Note that the operator does not support upgrading from standalone to distributed mode.
    servers: 4
## Supply number of volumes to be mounted per MinIO server instance.
volumesPerServer: 4
## Mount path where PV will be mounted inside container(s). Defaults to "/export".
mountPath: /data/export
## Sub path inside Mount path where MinIO starts. Defaults to "".
# subPath: /data
## This VolumeClaimTemplate is used across all the volumes provisioned for MinIO cluster.
## Please do not change the volumeClaimTemplate field while expanding the cluster, this may
## lead to unbound PVCs and missing data
volumeClaimTemplate:
  metadata:
    name: data
  spec:
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 1Ti
```

minioinstance is composed of statefulset, svc, pvc
