# kubernetes setup
k8s learnings

* Provision a bunch of nodes using Terraform/ Cloudformation (AWS)
* Setup kubernetes using a variety of k8s provisioning tools like - [kubespray](https://github.com/kubernetes-incubator/kubespray)
* Use a python script to generate a dynamic ansible inventory and feed it into kargo
* Once kubernetes is setup on all the hosts, the following will be setup-
    * Master nodes
    * nodes
    * etcd cluster (as a default can setup on master nodes only)
* From one of the master nodes, access etcd using-
```
$ etcdctl ls
Error:  malformed HTTP response "\x15\x03\x01\x00\x02\x02"
$ export ETCDCTL_ENDPOINT=https://127.0.0.1:2379
$ export ETCD_CERT_FILE=/etc/ssl/etcd/ssl/member-k8s-master-node-st-subnet-f416969d-0.pem
$ export ETCD_TRUSTED_CA_FILE=/etc/ssl/etcd/ssl/ca.pem
$ etcdctl ls
/calico
```
This does not work, as the `/registry` key is missing. Kubespray setups etcd API v3 as a default, while etcdctl uses v2 of the etcd API. To get this to work use the following-
```
$ export ETCDCTL_CACERT=/etc/ssl/etcd/ssl/member-k8s-master-node-st-subnet-f416969d-0.pem
$ export ETCDCTL_KEY=/etc/ssl/etcd/ssl/member-k8s-master-node-st-subnet-f416969d-0-key.pem
$ export ETCDCTL_CERT=/etc/ssl/etcd/ssl/member-k8s-master-node-st-subnet-f416969d-0.pem
$ export ETCDCTL_CACERT=/etc/ssl/etcd/ssl/ca.pem                                                        
$ export ETCDCTL_API=3
$ etcdctl member list
4e163df79473660f: name=etcd2 peerURLs=https://10.100.100.67:2380 clientURLs=https://10.100.100.67:2379 isLeader=false
b5c00149948f637b: name=etcd1 peerURLs=https://10.100.100.35:2380 clientURLs=https://10.100.100.35:2379 isLeader=false
cc4794098da00ecb: name=etcd3 peerURLs=https://10.100.200.59:2380 clientURLs=https://10.100.200.59:2379 isLeader=true
$ etcdctl cluster-health
member 4e163df79473660f is healthy: got healthy result from https://10.100.100.67:2379
member b5c00149948f637b is healthy: got healthy result from https://10.100.100.35:2379
member cc4794098da00ecb is healthy: got healthy result from https://10.100.200.59:2379
cluster is healthy
$ ETCDCTL_API=3 etcdctl get / --prefix --keys-only
/registry/configmaps/kube-system/extension-apiserver-authentication

/registry/deployments/kube-system/kubedns

/registry/deployments/kube-system/kubedns-autoscaler

/registry/events/kube-system/kubedns-3639568078-gjhqm.14cbbcad38a2193f

/registry/events/kube-system/kubedns-autoscaler-2999057513-rrnrm.14cbbcac52e597d4

...
```
From Kevin Qiu
```
ssh -D 8123 -f -C -q -N $BASTION_HOST
https_proxy=socks5://localhost:8123 kubectl cluster-info
Kubernetes master is running at https://10.100.100.106:6443
KubeDNS is running at https://10.100.100.106:6443/api/v1/namespaces/kube-system/services/kube-dns/proxy
```
# Custom Kubelet flags
Kubelet can be configured to evict pods/ delete dead containers to free up space via cli args -
```
--eviction-hard=nodefs.available<10%,nodefs.inodesFree<10%,imagefs.available<10%,imagefs.inodesFree<10%
```
Apply these in `/etc/kubernetes/kubelet.env` and restart the kubelt process via 
```
$ sudo systemctl restart kubelet # on CoreOS
```
Verify that the kubelet started up -
```
$ sudo journalctl -r -u kubelet
```
