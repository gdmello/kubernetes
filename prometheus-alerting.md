### Summary
Basic localhost alerting using prometheus, alert manager and node exporter (for host metrics only)

### Setup
* AlertManager - download and run alertmanager
```
$ alertmanager-0.7.1.linux-amd64/alertmanager --config.file=/home/gavin.dmello/new_wk_spc/alertmanager-0.7.1.linux-amd64/simple.yml
```
* Prometheus - download and run 
```
$ prometheus-2.0.0-beta.0.linux-amd64/prometheus --config.file=/home/gavin.dmello/new_wk_spc/prometheus-2.0.0-beta.0.linux-amd64/prometheus.yml --web.console.templates=/home/gavin.dmello/new_wk_spc/prometheus-2.0.0-beta.0.linux-amd64/consoles --web.console.libraries=/home/gavin.dmello/new_wk_spc/prometheus-2.0.0-beta.0.linux-amd64/console_libraries
 The console flags make the console `/consoles/` urls available.
```
Access prometheus at - http://localhost:9090/
* Node exporter - Export host/ system metrics
```
$ node_exporter-0.14.0.linux-amd64/node_exporter
```
Access node exporters at - http://localhost:9100/
* Configure prometheus to use the local alert manager, and setup a job to scrape the metrics from node_exporter
```
# prometheus-2.0.0-beta.0.linux-amd64/prometheus.yml
```

```

* The rule file looks like this-
```
ALERT TestAlert
    IF sum by (mode, instance) (node_cpu{mode="user", job="node"}) > 0.1
    FOR 5s
    LABELS {
        severity = "critical"
    }
```
prometheus 2.0 has a new YAML file format, so convert this to the new format with `promtool`-
```
$ ./prometheus-2.0.0-beta.0.linux-amd64/promtool update rules node.rules
Updating node.rules
```
This generates a new file, `node.rules.yml`, which looks like-
```
groups:
- name: node.rules
  rules:
  - alert: TestAlert
    expr: sum(node_cpu{job="node",mode="user"}) BY (mode, instance) > 0.1
    for: 5s
    labels:
      severity: critical
```
* Update the prometheus config to use the new file-
```
rule_files:
  - "node.rules.yml"
```
* View alerts at - http://localhost:9090/alerts
