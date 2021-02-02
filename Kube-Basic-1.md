# Kubernetes Basic

## Pre-requisite

A DockerHub account  
Log in on playground:

 - https://labs.play-with-k8s.com/

Line with # mark a command line command

## Kubernetes - basic

### Overview

In this section, you will: 
 - create a kubernetes cluster
 - setup a master node, along its workers
 - setup a pod with a nginx running 

### Command used

 - kubeadm: the install tool that will install and setup the environnemnt
 - kubectl: the command line tool to manipulate clusters, it's the one that interact with the clusters
    
 - kubeadm init: initialize the environnement
 - kubeadm join: link a worker node to a mster node
 - kubectl apply: apply a configuration to a node from a yaml file
 - kubectl get: get information on node or pods
 - kubectl delete: delete a kubernetes element by name
 - kubectl logs: output the log of an object
 - kubectl describe: output all the detail of an object, log and characteristic
 - kubectl run: launch a pod in the cluster

### Hands on - Pods

Control Plane --> 1,n Node master 1,1 --> 1,n Node Child 1,1 --> 1,n pod

On a first instance, setup a master node (this one act as the monitor, seer, supervisor of other instances or "workers" and handle network etc...), these command may vary, the IP will probably be different and kubernetes will prompt these commands  

    # kubeadm init --apiserver-advertise-address $(hostname -i) --pod-network-cidr 10.5.0.0/16
    # kubectl apply -f https://raw.githubusercontent.com/cloudnativelabs/kube-router/master/daemonset/kubeadm-kuberouter.yaml

copy the join command which will look like this, it is prompted in the first command
    
    # kubeadm join ip:port --token i75xvf.wfg8so9nw0btth05 \
        --discovery-token-ca-cert-hash sha256:e73b4b2fb734df3a89ee7da9e0fb2cfc895db2bb10ca83db040f38f4bceecdf8
    ex: kubeadm join 192.168.0.13:6443 --token 09lrgl.fs555p6fx0c0e8h7 \
    --discovery-token-ca-cert-hash sha256:7fc8a35c373522c0b8968760643fd5c17d8e203fcc8142a37bdedda5c95cc3fa

You can use this command to add worker nodes, create a new instance and enter this command to start a worker node
In the master node check that the worer is displayed

    # kubectl get node

this will list one node as master, the other as none (a worker)

In the master node, create a yaml cofiguration file

    # cat > podnginx.yaml

```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - image: nginx
      name: nginx
      ports:
      - containerPort: 80
```

This will define a pod to be created and assigned to one of the worker node, create the pod by applying the configuration
Note that indentation will break the yaml file, it's indented using 2 spaces

    # kubectl apply -f podnginx.yml
    # kubectl get pod

You should now see a pod created with a status of running. If you have a status like evicted, delete the pod and try again, the name is listed in the command

    # kubectl get pod
    # kubectl delete pod/nginx

In order to get more information on a running pod (the first one is an enriched list of the pods, the 2nd and 3rd gives the log of a specific pod or object)

    # kubectl get pod -o wide
    # kubectl logs nginx
    # kubectl describe pod/nginx

We can also start a pod without a yaml file 

    # kubectl run nginx --image=nginx
    
And finaly we can ask it to directly generate the yaml file from this run command or just display it in the terminal wihout making a file
    
    # kubectl run nginx --image=nginx --dry-run=client -o yaml
    # kubectl run nginx --image=nginx --dry-run=client -o yaml > nginx-imp.yaml

--dry-run=client shows you the object without running it by default it will say pod created (dry run)
-o (shorthand for --output) is used to specify the output format (here yaml)

You can connect to a pod that is currently running in the workers

    # kubectl exec -it <pod_name> -- sh