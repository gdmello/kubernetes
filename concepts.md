node - a host in kubernetes which will container one or more application containers
master node - specialized nodes which enable interaction with the kubernetes cluster.

The `Kubernetes Control Plane` is a collection of processes running on a cluster-
* Kubernetes Master - consists of 
  * `kuber-apiserver`, 
  * `kube-controller-manager` - a daemon which embeds core control loops. A controller is a control loop which watches cluster state via the apiserver and makes changes, based on that state, to bring the actual state in conformity to the shared state.
  * `kube-scheduler`
* Non-master Nodes - consist of - 
  * `kubelet` - communicates with the kubernetes master
  * `kube-proxy` - 
