# Kubernetes Basic

## Pre-requisite

A DockerHub account  
Log in on playground:

 - https://labs.play-with-k8s.com/

Line with # mark a command line command

## Kubernetes - basic

### Overview

In this section, you will: 
- Lean the component involved in storage solution
- Learn how to use a claim


### Command used
    
    - kubectl get: get information on object
    - kubectl create: create a new kube object
    - kubectl apply: apply a configuration to a node from a yaml file

---
### Theory - PV - PVC

Pv and PVC = Peristant volume and persistent volume claim

Persistent Volume
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-volume1
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

PersistentVolumeClaim
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

Un claim déclare qu'il a besoin d'un storage avec une liste de charactéristique, ex: un label, une capacité

kubernetes va allez lui chercher un volume qui correspondra au mieux à ce claim. 
le pod utilisera donc le volume que kubernetes lui à retourner en fonction du claim qu'on a donner au pod

Un pod est associé à un ou plusieurs volume mais un volume est associé à un claim

la liaison claim/volume n'est pas spécifique et peut changer

Les PV sont global au cluster, pas de namespace
Les PVC sont global mais ont un namespace

Sur un pod on peut rajouter les volumes suivants   

```
spec: 
  volumes:
    - names: pv-storage-example
      persistentVolumeClaim:
        claimName: pv-claim
    - names: pv-storage-aws
      persistentVolumeClaim:
        claimName: pv-claim-aws
  containers
    - name: nginx
      ...
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: pv-storage-example
        - mountPath: "/usr/share/nginx/aws/img"
          name: pv-storage-aws
```

Ici le premier volume claim fera référence aux fichiers source du site web (pv-storage-example avec pv-claim)
Le deuxième volume pourra être lié à un bucket aws pour le stockage d'image (pv-storage-aws avec pv-claim-aws)
Du coup sur le pod avec l'image nginx, on pourra avoir le site et ses image comme si tout était en local sur nginx/html et nginx/aws/img

---

### Hands on - Ingress
