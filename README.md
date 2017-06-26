# kubernetes
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
