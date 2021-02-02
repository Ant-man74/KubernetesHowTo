# Kubernetes cheatsheet

## The best command

 - kubectl get: get information on object
 - kubectl apply: apply a configuration from a yaml file
 - kubectl delete: delete a kubernetes element by name
 - kubectl run: execute an object (pod, replicaset, deployement etc)
 - kubectl describe: output all the detail of an object, log and characteristic

## Manipulate Nodes if something has gone wrong, use with caution

 - systemctl start kubelet
 - systemctl stop kubelet
 - systemctl restart kubelet
 - journalctl -u kubelet

## The main object

All object excluding nodes can have yaml config file generated for them

 - Master node: A node that oversee a bunch of worker node. Commands to create, delete, update pods or object are executed there
 - Worker node: A node that contains pods and report to the master node. Has no knowledge of other worker node
 - Pods: The content of a worker node, will execute containerId images (docker or other)
 - Namespace: Categorization element that can be created to regroup kubernetes element, specified in metadata of elements
 - ReplicaSet: An element that ensure that a certain number of pods are always available no matter on which node
 - DaemonSet: An element that ensure that specific pods are alway running on each worker node
 - Deployement: An object that will create pods and replica in order to reach a desired state, THE main object which are used instead of defining each object individually. View this as the main program that will instantiate classes (kube object) in order for the program to run. When the deployement template config changes, the deployement will rollout the change (ex: changing a version of a pod image). As you can see
 - PV: Persistent Volume are storage object, they can have a wide variety of source, they are global to the cluster and everybody can see them
 - PVC: Persistent Volume Claim, they define the characteristic of a wanted storage object. They dont reference a specific volume. Kubernetes will match a claim to an appropriate volume. A pod will need to declare which claim he wants to use
 - Service: An element enabling communication bridges between workers, as a rest interface