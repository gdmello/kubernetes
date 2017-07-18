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
* Node exporter - Export host/ system metrics
```
$ node_exporter-0.14.0.linux-amd64/node_exporter
```
* Configure prometheus to use the local alert manager, and setup a job to scrape the metrics from node_exporter
```
# prometheus-2.0.0-beta.0.linux-amd64/prometheus.yml
```
rule_files:
  - "node.rules.yml"
  

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'
    scrape_interval: 5s
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node'
    scrape_interval: 2s
    static_configs:
    - targets: ['localhost:9100']
    
alerting:
  alertmanagers:
  - static_configs:
    - targets: ['localhost:9093']
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
