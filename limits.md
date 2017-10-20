Limits
======

Pods, namespaces in kubernetes can be assigned resource limits. Resources are CPU and memory (RAM). 
Resource limits are specified via -
* Resource Requests - The amount of CPU/Memory the pod is guaranteed
* Resource Limits - The maximum amount of CPU/Memory the pod can consume.

When Resource Limits > Resource Requests, the pod (i.e. all containers within the pod) 
will be guaranteed resources specified in the Request and can exceed these values upto a
maximum of Resource Limits.

https://github.com/kubernetes/community/blob/master/contributors/design-proposals/node/resource-qos.md

If a pod exceeds the Resource Limit, it can be terminated and restarted (depending on pod restart policy).
