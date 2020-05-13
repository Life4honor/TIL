# Amazon Elatic Kubernetes Service

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

Sample scirpt to create `vtflow` cluster with managed node group is shown below

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

Create cluster based on config file with `eksctl create cluster -f cluster.yaml` command

```yaml
# cluster.yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: vtflow
  region: ap-northeast-2

# Config file need to manage nodegroup explicitly
nodeGroups:
  - ...

managedNodeGroups:
  - ...
```

## Delete EKS Cluster

### CLI

...

### Config file

...

## Reference

- [Usage](https://eksctl.io/usage/creating-and-managing-clusters/)
- [Config file Example](https://eksctl.io/examples/reusing-iam-and-vpc/)
