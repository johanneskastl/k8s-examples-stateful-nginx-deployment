# k8s-examples-stateful-nginx-deployment
Deploying nginx with website data from a persistent storage (on the host)

On your node(s), create the directory /opt/kubernetes-storage/nginx and add a simple file called `index.html`.

This file can contain whatever content you want to have, for a starter try this:

```
If you can read this, nginx delivered from the hostPath volume...
```

Then create (in this order):
- the PersistentVolume (using `kubectl apply -f 01_PersistentVolume-hostPath-nginx.yaml`)
- the PersistentVolumeClaim (using `kubectl apply -f 02_PersistentVolumeClaim-hostPath-nginx.yaml`)
- a pod using that PersistentVolumeClaim (using `kubectl apply -f 03_Pod-nginx-with-pvc.yaml`)

Afterwards, run curl or wget against your pod's cluster-internal IP:

```
$ kubectl describe pod/pod-nginx-with-pvc | grep "^IP:"
IP:           10.32.0.36
$ curl 10.32.0.36
If you can read this, nginx delivered from the hostPath volume...
$
```

