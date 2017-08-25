Introduction
============
Notes to deploy stolon, a Postgres HA manager.

Requirements
============
* k8s cluster (using stackpoint.io)
* etcd cluster
* stolon

Setup
=====

k8s cluster
-----------
Use `kubespray` or a commercial offering like `stackpoint`

etcd cluster
------------
Use CoreOS `etcd-operator` to install a 3 node cluster

  $ git clone https://github.com/kubernetes/charts.git
  $ cd charts/etcd-operator
  $ vim values.yaml
  
  Set `cluster:enabled` and further down below use provisioner as AWS `provisioner: kubernetes.io/aws-ebs`

    $ helm install --name etcd-cluster-2 etcd-operator/
      NAME:   etcd-cluster-2
      LAST DEPLOYED: Fri Aug 25 18:02:41 2017
      NAMESPACE: default
      STATUS: DEPLOYED

      RESOURCES:
      ==> v1beta1/Deployment
      NAME                          DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
      etcd-cluster-2-etcd-operator  1        1        1           0          0s


      NOTES:
      Not enabling cluster, the ThirdPartResource must be installed before you can create a Cluster. Continuing rest of normal deployment.

      1. etcd-operator deployed.
        If you would like to deploy an etcd-cluster set cluster.enabled to true in values.yaml
        Check the etcd-operator logs
          export POD=$(kubectl get pods -l app=etcd-cluster-2-etcd-operator --namespace default --output name)
          kubectl logs $POD --namespace=default
    $ helm upgrade etcd-cluster-2 etcd-operator/
      Release "etcd-cluster-2" has been upgraded. Happy Helming!
      LAST DEPLOYED: Fri Aug 25 18:04:22 2017
      NAMESPACE: default
      STATUS: DEPLOYED

      RESOURCES:
      ==> v1beta1/Deployment
      NAME                          DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
      etcd-cluster-2-etcd-operator  1        1        1           1          1m

      ==> v1beta1/Cluster
      NAME          KIND
      etcd-cluster  Cluster.v1beta1.etcd.coreos.com


      NOTES:
      1. Watch etcd cluster start
        kubectl get pods -l etcd_cluster=etcd-cluster --namespace default -w
      2. Confirm etcd cluster is healthy
        $ kubectl run --rm -i --tty --env="ETCDCTL_API=3" --env="ETCDCTL_ENDPOINTS=http://etcd-cluster-client:2379" etcd-test --image quay.io/coreos/etcd --restart=Never -- /bin/sh -c 'watch -n1 "etcdctl  member list"'

      3. Interact with the cluster!
        $ kubectl run --rm -i --tty --env ETCDCTL_API=3 etcd-test --image quay.io/coreos/etcd --restart=Never -- /bin/sh
        / # etcdctl --endpoints http://etcd-cluster-client:2379 put foo bar
        / # etcdctl --endpoints http://etcd-cluster-client:2379 get foo
        OK
        (ctrl-D to exit)
      4. Optional
        Check the etcd-operator logs
        export POD=$(kubectl get pods -l app=etcd-cluster-2-etcd-operator --namespace default --output name)
        kubectl logs $POD --namespace=default
    $ kc get po | grep etcd
      etcd-cluster-0000                               1/1       Running   0          8m
      etcd-cluster-0001                               1/1       Running   0          8m
      etcd-cluster-0002                               1/1       Running   0          7m
      etcd-cluster-2-etcd-operator-1952289938-2jp9x   1/1       Running   0          9m



Postgres HA with stolon
-----------------------

    $ git clone https://github.com/lwolf/stolon-chart.git
    $ cd stolon-chart
    $ vim stolon/values.yaml
  
  
  Set the etcd endpoints, `endpoints: "http://etcd-cluster-client:2379"`


    $ kc get svc  | grep etcd
      etcd-cluster                 None         <none>        2380/TCP   11m
      etcd-cluster-client          10.3.0.108   <none>        2379/TCP   11m
  

  

  
