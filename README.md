## Kubernetes-Demo

Demo for running a Kubernetes cluster within a "kind" (Kubernetes in Docker) instance on a Linux VM. We're using colima here, which is a developer version of the Lima VM for running Linux on MacOS architecture.

## Initial Setup

First, install colima (Lima VM for MacOS) and kind (Kubernetes in Docker). This will allow you to run a local Kubernetes instance within the Docker runtime. Within this node, you will use an internal Docker runtime to deploy containers as Kubernetes pods. 

```
$ brew install colima docker docker-compose kubectl kind
$ kind version
$ colima start
```

If you want to tweak allocated resources, you can with:

```
$ colima start --edit
# or:
$ vi ~/.colima/default/colima.yaml
```

## Setting up Persistent Storage

You will need to use the provided kind-config.yaml to mount a local directory for persistent storage.

```
$ kind create cluster --config ./kind/kind-config.yaml
```

Now your local MacOS dir is mounted within kind. After this, we create a PersistentVolume and PersistentVolumeClaim to mount it from the kind/k8s node into the k8s pods/containers.

```
$ kubectl apply -f ./k8s/postgres-pv.yaml
$ kubectl apply -f ./k8s/postgres-pvc.yaml
```

PV is not attached to PVC. Before you attach the PVC to an actual database pod, please update the credentials from defaults. Then:

```
$ kubectl apply -f ./k8s/database.yaml
```

All set. Connect via CLI with:

```
$ kubectl exec -it deployment/postgres -- psql -U <database-username> -d <database-name>
```
