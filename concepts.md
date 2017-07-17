node - a host in kubernetes which will container one or more application containers
master node - specialized nodes which enable interaction with the kubernetes cluster.

## Kubernetes Control Plane
This is a collection of processes running on a cluster-
* Kubernetes Master - consists of 
  * `kuber-apiserver`, 
  * `kube-controller-manager` - a daemon which embeds core control loops. A controller is a control loop which watches cluster state via the apiserver and makes changes, based on that state, to bring the actual state in conformity with the shared state.
  * `kube-scheduler`
* Non-master Nodes - consist of - 
  * `kubelet` - communicates with the kubernetes master
  * `kube-proxy` - a network proxy per node
  
## Basic Kubernetes Objects
These include -
* Pods - the smallest deployable object in kubernetes. Can consist of one or more containers.
* Services - Since `Pods` are mortal, `Services` ensure that clients can reach one or more `Pods` (via label selectors). 
  * whenever the pods in a service are updates, the `Endpoints` api is updated, to map the service to a set of `live` pods.
* Namespaces
* Volumes
* Controllers
  * ReplicaSet
  * DaemonSet
  * StatefulSet
  * Deployment
  * Job
  
