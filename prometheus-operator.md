### Steps to setup prometheus via the 'Prometheus Operator'
The operator creates 3 third party resources (TPR)-
* Prometheus
* ServiceMonitor
* AlertManager

Start by creating a service with the resource type - `Prometheus`.
```bash

```
This creates a prometheus server, as a `StatefulSet` service in a pod.
A `ServiceMonitor` can be configured for a prometheus server (started above) via labels. The `operator` continuously looks up and updates changes to these `ServiceMonitor` resources as well as to the `Prometheus` TPR itself.

