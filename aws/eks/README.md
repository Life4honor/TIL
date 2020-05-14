# Amazon Elastic Kubernetes Service

Create eks cluster with eksctl

## Prerequisite

- Configured AWS credentials with `~/.aws/credentials`

## Installation

### Ubuntu 19.10

```sh
# eksctl
$ curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
$ sudo mv /tmp/eksctl /usr/local/bin

# aws-iam-authenticator
$ curl -o aws-iam-authenticator https://amazon-eks.s3.us-west-2.amazonaws.com/1.16.8/2020-04-16/bin/linux/amd64/aws-iam-authenticator
$ chmod +x ./aws-iam-authenticator
$ sudo mv ./aws-iam-authenticator /usr/local/bin/
```

### MacOS - Catalina

Homebrew will install `eksctl` and also `aws-iam-authenticator`

```sh
$ brew tap weaveworks/tap
$ brew install weaveworks/tap/eksctl
```

## Create EKS Cluster

### CLI

Create a cluster with `eksctl create cluster` command

> kubernetes configuration will be added to `~/.kube/config` automatically by default

```sh
# eksctl create cluster --help for more information
$ eksctl create cluster
```

Sample scirpt to create `vtflow` cluster with a managed node group is shown below

```sh
#!/bin/bash

CN=${CN:=vtflow} # ClusterName
REGION=${REGION:=ap-northeast-2} # Region
TYPE=${TYPE:=m5.large} # Node Instance Type
NODES=${NODES:=2} # Initiate the num of nodes
MIN=${MIN:=2} # Min num of nodes
MAX=${MAX:=2} # Max num of nodes

# eksctl create cluster --help to see more options
eksctl create cluster --name $CN --region $REGION --node-type $TYPE --nodes $NODES --nodes-min $MIN --nodes-max $MAX --full-ecr-access --alb-ingress-access --managed
```

### Config file

Create a cluster based on config file with `eksctl create cluster -f cluster.yaml` command

```yaml
# cluster.yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: <ClusterName>
  region: <Region>

# Config file need to manage nodegroup explicitly
nodeGroups:
  - ...

managedNodeGroups:
  - ...
```

Sample configuration for `vtflow` cluster with a managed node group is shown below

```yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig
metadata:
  name: vtflow
  region: ap-norteast-2
managedNodeGroups:
  - name: managed-ng-1
    minSize: 2
    maxSize: 2
    desiredCapacity: 2
    volumeSize: 20
    privateNetworking: true
    instanceType: m5.large
```

## Delete EKS Cluster

### CLI

Delete a cluster with `eksctl delete cluster` command

```sh
$ eksctl delete cluster -w -n vtflow -r ap-northeast-2
```

### Config file

Delete a cluster based on config file with `eksctl delete cluster -w -f cluster.yaml` command

```sh
$ eksctl delete cluster -w -f cluster.yaml
```

## Reference

- [Creating ans managing clusters](https://eksctl.io/usage/creating-and-managing-clusters/)
- [Config file example](https://eksctl.io/examples/reusing-iam-and-vpc/)
- [Managing nodegroups](https://eksctl.io/usage/managing-nodegroups/)
