# Ceph

## Component

A Ceph Storage Cluster requires at least one Ceph Monitor, Ceph Manager, and Ceph OSD (Object Storage Daemon). The Ceph Metadata Server is also required when running Ceph File System clients

### Monitors

`Ceph Monitor` maintains maps of the cluster state, including the monitor map, manager map, the OSD map, the MDS map, and the CRUSH map.

### Managers

Ceph Manager Daemon is responsible for keeping track of runtime metrics and the current state of the Ceph cluster, including storage utilization, current performance metrics, and system load

### Ceph OSDs

`Ceph OSD(object storage daemon)` stores data as objects on a storage node. At least 3 Ceph OSDs are normally required for redundancy and high availability

### MDSs

MDS(metadata server) stores metadata only for Ceph File System

## Installation - Rook
Rook deploys and manages Ceph clusters running in Kubernetes, while also enabling management of storage resources and provisioning via Kubernetes API

In order to configure the Ceph storage cluster, at least one of these local storage options are required:

- Raw devices (no partitions or formatted filesystems)
- Raw partitions (no formatted filesystem)
- PVs available from a storage class in block mode

```sh
$ lsblk -f

NAME                  FSTYPE      LABEL UUID                                   MOUNTPOINT
vda
└─vda1                LVM2_member       eSO50t-GkUV-YKTH-WsGq-hNJY-eKNf-3i07IB
  ├─ubuntu--vg-root   ext4              c2366f76-6e21-4f10-a8f3-6776212e2fe4   /
  └─ubuntu--vg-swap_1 swap              9492a3dc-ad75-47cd-9596-678e8cf17ff9   [SWAP]
vdb
```
If the `FSTYPE` field is not empty, there is a filesystem on top of the corresponding device. In this case, you can use vdb for Ceph and can’t use vda and its partitions.

```sh
git clone --single-branch --branch release-1.3 https://github.com/rook/rook.git
cd rook/cluster/examples/kubernetes/ceph

# Deploy Rook Operator
kubectl create -f common.yaml
kubectl create -f operator.yaml

# Create a Rook Ceph Cluster
kubectl create -f cluster.yaml
```

