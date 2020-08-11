# MongoDB

## Terminology

### Replicaset

TBD

### Shard

TBD

## Docker

### Install

```sh
$ docker pull mongo
```

### Run

```sh
# Server
$ docker run --name some-mongo -d mongo:tag
```

```sh
# Client
$ docker run -it --network some-network --rm mongo mongo --host some-mongo test
```

## K8S

### Operator

### MongoDB CRD

MongoDB Community Operator

- ReplicaSet

```yaml
apiVersion: mongodb.com/v1
kind: MongoDB
metadata:
  name: mongodb
  namespace: mongodb
spec:
  members: 3
  type: ReplicaSet
  version: "4.2.6"
  security:
    authentication:
      enabled: true
      modes: ["SCRAM"]
  users:
    - name: my-user
      db: admin
      passwordSecretRef:
        name: my-user-password
      roles:
        - name: clusterAdmin
          db: admin
        - name: userAdminAnyDatabase
          db: admin
```
