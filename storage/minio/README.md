# MinIO

## Installation

### Docker

```sh
$ docker pull minio/minio
```

## Run

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
