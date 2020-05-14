# k8s-examples-stateful-nginx-deployment
Deploying nginx with website data from a persistent storage (on the host)

## Prepare local storage

On your node(s), create the directory `/opt/kubernetes-storage/nginx/` and add a simple file called `index.html`.

This file can contain whatever content you want to have, for a starter try this:

```
If you can read this, nginx delivered from the hostPath volume...
```

Then create (in this order):
- the PersistentVolume (using `kubectl apply -f 01_PersistentVolume-hostPath-nginx.yaml`)
- the PersistentVolumeClaim (using `kubectl apply -f 02_PersistentVolumeClaim-hostPath-nginx.yaml`)

# Single pod using the persistent volume

Create a pod using the previously prepared PersistentVolumeClaim (using `kubectl apply -f 03_Pod-nginx-with-pvc.yaml`)

Afterwards, run curl or wget against your pod's cluster-internal IP:

```
$ kubectl describe pod/pod-nginx-with-pvc | grep "^IP:"
IP:           10.32.0.36
$ curl 10.32.0.36
If you can read this, nginx delivered from the hostPath volume...
$
```

## StatefulSet with persistent volume

Create the StatefulSet using `kubectl apply -f 04_StatefulSet-nginx-with-pvc.yaml`.

Afterwards, run curl or wget against your pod's cluster-internal IP:

```
$ kubectl describe pod/nginx-statefulset-0|grep "^IP"
IP:           10.32.0.37
$ curl 10.32.0.37
If you can read this, nginx delivered from the hostPath volume...
$
```

## Service using the StatefulSet

Create a service using the StatefulSet with `kubectl apply -f 05_nginx-service-statefulset.yaml`.

Afterwards, run curl or wget against your service's ClusterIP:

```
$ kubectl get svc|grep nginx-service-statefulset
nginx-service-statefulset   ClusterIP   10.100.166.24   <none>        80/TCP    46s
$ curl 10.100.166.24
If you can read this, nginx delivered from the hostPath volume...
$
```

# Ingress for the StatefulSet service

Deploy a ingress extension for the `nginx-service-statefulset` service using ` kubectl apply -f 06_nginx-ingress-statefulset.yaml`.

This will listen on any IP or hostname with the `/statefulset` path, e.g. using 192.168.0.2 as the external IP:
```
$ curl 192.168.0.2/statefulset
If you can read this, nginx delivered from the hostPath volume...
$
```
