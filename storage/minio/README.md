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
