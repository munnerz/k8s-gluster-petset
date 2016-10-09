# GlusterFS PetSet

This contains manifests and info for running GlusterFS server as a petset under Kubernetes

## TODO:

* Run heketi, configure auto provisioner
* Find a way to make an endpoints object in each namespace (??)

## Checklist

* Add cluster DNS to hosts resolv.conf so hosts can resolve brick hostnames
* Create a non-headless service so that an Endpoints object is created to use when mounting a GlusterFS vol
* Create a headless service so that pets have stable DNS name
* Adjust the `vol-brick-*.yaml` files to provide appropriate stable storage for glusterfs

## Creating volumes

When creating volumes, be sure to use the full DNS name of each brick, else the hosts will not be able to
resolve brick names eg.

```
$ gluster volume create vol_name glusterfs-server-0.glusterfs.kube-system.svc.cluster.local:/brick/vol_name glusterfs-server-1.glusterfs.kube-system.svc.cluster.local:/brick/vol_name
```