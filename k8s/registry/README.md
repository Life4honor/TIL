# Private Registry for Kubernetes Cluster

## ConfigMap

`config.yml` is a configuration file for private registry setup.

```yaml
# registry-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: registry-config
  namespace: registry
data:
  config.yml: |
    version: 0.1
    log:
      fields:
        service: registry
    storage:
      cache:
        blobdescriptor: inmemory
      filesystem:
        rootdirectory: /var/lib/registry
    http:
      addr: :5000
      headers:
        X-Content-Type-Options: [nosniff]
    auth:
      htpasswd:
        realm: basic-realm
        path: /etc/registry
    health:
      storagedriver:
        enabled: true
        interval: 10s
        threshold: 3
```

## Pod

Deploy Pod with following credential for authentication. 

- `Username : life4honor`
- `Password: Passw0rd.`

```yaml
# registry.yaml
apiVersion: v1
kind: Pod
metadata:
  name: registry
  namespace: registry
spec:
  initContainers:
    - name: auth-generator
      image: registry:2
      command: ["htpasswd"]
      args: ["-bBc","/etc/registry", "life4honor", "Passw0rd."]
      volumeMounts:
        - mountPath: /etc
          name: registry-credentials
  containers:
    - name: private-registry
      image: registry:2
      volumeMounts:
        - mountPath: /var/lib/registry
          name: registry-volume
        - mountPath: /etc/docker/registry/
          name: registry-config
        - mountPath: /etc
          name: registry-credentials
  volumes:
    - emptyDir: {}
      name: registry-credentials
    - hostPath:
        path: /var/lib/registry
        type: DirectoryOrCreate
      name: registry-volume
    - configMap:
        name: registry-config
      name: registry-config
```

## Run a Private Registry - Kustomize

Deploy all resources related to private registry with command below

```sh
$ kustomize build | kubectl apply -f -
```

All resources are defined in `kustomization.yaml`

```yaml
# kustomization.yaml
resources:
  - registry-config.yaml
  - registry.yaml
```

## Docker Login & Push & Pull

```sh
# Docker Login
$ docker login ${REGISTRY_IP}:${PORT}

# Pull Docker IMAGE
$ docker pull ${IMAGE}

# Push Docker IMAGE
$ docker push ${IMAGE}
```