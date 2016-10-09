# GlusterFS PetSet

** NOTE: THIS IS AN EXTREMELY PRE-ALPHA, NOT-BETA DEFINITELY NOT PRODUCTION READY RELEASE! **

This contains manifests and info for running GlusterFS server as a petset under Kubernetes

## Requirements

* Kubernetes 1.4+ with PetSet alpha enabled
* Each Kubernetes node must include kube-dns as one of it's nameservers

## Getting started

First we must create the volumes that we're going to use for our GlusterFS server. If you are running
in an environment that already supports dynamic provisioning of disks, this step can be skipped.
In the `kubernetes` directory there are two volume definitions, `vol-brick-{1,2}.yaml`, that define
a physical volume as a host path. They are identical aside from name, and the pod anti-affinity on the PetSet
will ensure that two hosts will never mount the same host path (which would probably cause havoc!)

```
$ kubectl create -f kubernetes/vol-brick-1.yaml -f kubernetes/vol-brick-2.yaml
```

This step can be repeated for as many nodes as you plan to have in your GlusterFS cluster, and more can be
added in future.

The PetSet yaml contains two additional services - one is a headless service, that in combination with the PetSet
will create stable DNS names for each GlusterFS server in the cluster
(eg. `glusterfs-server-0.glusterfs.kube-system.svc.cluster.local`). This is neccessary as each pod in the PetSet's
IP address may change between restarts (as the pod is scheduled onto different nodes etc).

The second service is of type ClusterIP, and is used to generate an Endpoints definition for our pods to refer
to when mounting glusterfs volumes.

```
$ kubectl create -f kubernetes/petset-glusterfs-server.yaml
```

## Using the cluster

Currently, the PetSet will not create any volumes, nor will it automatically peer new nodes into an existing cluster.
Therefore, it's neccessary that you manually exec into each glusterfs node to perform initialisation. For more information,
see the official GlusterFS quick-start guide here: http://gluster.readthedocs.io/en/latest/Quick-Start-Guide/Quickstart/#step-4-configure-the-trusted-pool

In future it is intended that more of these steps will be automated away.

When creating volumes, be sure to use the full DNS name of each brick, else the hosts will not be able to
resolve brick names eg.

```
$ gluster volume create vol_name glusterfs-server-0.glusterfs.kube-system.svc.cluster.local:/brick/vol_name glusterfs-server-1.glusterfs.kube-system.svc.cluster.local:/brick/vol_name
```

## Future work/TODO

* Run heketi in the cluster & configure as auto-provisioner
* Find a way to make an endpoints object in each namespace
