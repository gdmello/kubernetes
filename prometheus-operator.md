### Steps to setup prometheus via the 'Prometheus Operator'
The operator creates 3 third party resources (TPR)-
* Prometheus
* ServiceMonitor
* AlertManager

Start by creating a service with the resource type - `Prometheus`.
```bash

```
This creates a prometheus server, as a `StatefulSet` service in a pod.
