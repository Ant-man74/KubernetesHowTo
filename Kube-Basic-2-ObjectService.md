
# Kubernetes Basic

## Pre-requisite

A DockerHub account  
Log in on playground:

 - https://labs.play-with-k8s.com/

Line with # mark a command line command

## Kubernetes - basic

### Overview

In this section, you will: 
- create a namespace
- Learn different element and their uses
- create a service
- expose your deployment to access it on your web browser

### Command used
    
    - kubectl get: get information on object
    - kubectl create: create a new kube object
    - kubectl config: set a config on your current kube session (like a default namespace)
    - kubectl apply: apply a configuration to a node from a yaml file
    - kubectl expose: create a service to apply to a deployment
    - kubectl get all -l key=value: list all object with the corrsponding key:value as label

---
### Hands on - Namespace

As a general rule of thumb, you can create, for most object a yaml configuration file. In <Kube-Basic-1.md> we manipulated node and pods, but there are other kube object that can be usefull
the --dry-run=client -o yaml can be applied to most object

A namespace is a categorization object used to organize cluster, just like all kube object the same command apply to manipulate, generate and create it. It is part of the metadata object in the config of kubernetes object. You can also set the namespace for all subsequent kubecommand

    # kubectl get namespaces
    # kubectl create namespace test
    # kubectl create namespace test --dry-run=client -o yaml

```
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: null
  name: test
spec: {}
status: {}
```

    # kubectl config set-context --current --namespace=test

Now all subsequent object will have the namespace set as 'test', note that only object created in the session will have it, it will not appear in yaml files and object created from yaml file will automaticly join the namespace, enven if it is omitted in the yaml file
  
Can add namespace specification to a pod yaml or as many label (as key:value pair) as needed, if you set a namespace by default, get pod wil lsearch that namespace only, the option --all-namespaces can be used to bypass it

```
kind: Pod
metadata:
  name: nginx
  namespace: demo
  label:
    app:nginx
```

    # kubectl get pod --namespace test
    # kubectl get pods --all-namespaces

---
### Hands on - ReplicaSet, DaemonSet, Deplyment

A ReplicaSet allow you to define a number of pod to duplicate, the same command (create, get and run) apply to it  
ex: replicate 3 time all pod with label nginx or with namespace test (no matter in which worker node they are)

Daemon set, enable the replication of pods on workers, the same command (create, get and run) apply to it  
ex: always have one pod in each worker with logging capability (one in each worker node)

- ReplicaSet ensures that the number of pods of an application is running on the correct scale as specified in the conf file
- DaemonSet will ensure that one copy of pod defined in our configuration will always be available on every worker node.

   - While they are similar, DaemonSet are on the same level as a deployement object (it handle the lifecycle of pods) rather than just ensuring that a number of pods are met like ReplicaSet (kind of "dumb", it only create the pod to meet a quota) see <https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/#deployments>

Deployement allow the definition of multiple pods (image version management, update of pods, rollback...) it also allow the definition of replica set for the pods it handle. If the spec.template section of a deployement file change, it will rollout the update across all the pod it manage. This is the main object that is used in environnements but it make use of most of the previous bricks.  

    # kubectl create deployment <name> --image=nginx -o yaml > deploy.yaml
    # kubectl apply -f vote_deployment.yaml

```
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: "2020-12-16T14:49:35Z"
  generation: 1
  labels:
    app: vote
    manager: kubectl
  name: vote
  namespace: default
  resourceVersion: "12542"
spec:
  progressDeadlineSeconds: 600
  replicas: 3
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: vote
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: vote
    spec:
      containers:
      - image: instavote/vote
        imagePullPolicy: Always
        name: vote
        ports:
        - containerPort: 80
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30

```

---
### Hands on - Service

Service
A Rest Gateway that can expose port of pods and allow communication between workers and their pods
These two command are equivalent

    # kubectl expose deployment vote --type=NodePort --name=srv-expose --dry-run -o yaml > srv-expose.yaml
    # kubectl create service nodeport vote --tcp=80:80 --dry-run=client -o yaml
    
```
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: vote
    manager: kubectl
  name: srv-expose
spec:
  ports:
  - name: 31001-80
    port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 31001
  selector:
    app: vote
  type: NodePort
```

port: 80 = Port of the service
targetPort: 80 = Port of the Pod (80 by default, optional)
nodePort: 31001 = Port of the node

This will allow you to see the kubernetes app in your browser at the ip ip:31001
