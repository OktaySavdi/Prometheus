### Service Discovery

Edit /etc/prometheus/prometheus.yml file
```sh
vi /etc/prometheus/prometheus.yml
```
Update - job_name: "demo" so that it looks like as below:
```yaml
- job_name: "demo"
  file_sd_configs:
    - files:
        - /etc/prometheus/file-sd.json
  relabel_configs:
    - source_labels: [env]
      regex: prod
      action: keep
```
Change below lines under - job_name: "demo":
```yaml
relabel_configs:
  - source_labels: [env]
    regex: prod
    action: keep

to

relabel_configs:
  - source_labels: [team, env]
    regex: api;prod
    action: keep
```
Add below lines under - job_name: "demo":
```yaml
relabel_configs:
  - source_labels: [team]
    regex: (.*)
    action: replace
    target_label: organization
    replacement: org-$1
```
Add below lines under - job_name: "demo":
```yaml
relabel_configs:
  - regex: type
    action: labeldrop
```
Add below lines under - job_name: "demo":
```yaml
    relabel_configs:
      - regex: __meta_(.*)__
        action: labelmap
        replacement: $1
```
Add below lines under - job_name: "demo":
```yaml
metric_relabel_configs:
  - source_labels: [__name__]
    regex: node_network_transmit_drop_total
    action: drop
```
Add below lines under - job_name: "demo":
```yaml
metric_relabel_configs:
  - source_labels: [__name__]
    regex: node_network_receive_bytes_total
    action: replace
    target_label: __name__
    replacement: node_network_rx_bytes_total
```
Add below lines under - job_name: "demo":
```yaml
metric_relabel_configs:
  - regex: fstype
    action: labeldrop
```
Add below lines under - job_name: "demo":
```yaml
metric_relabel_configs:
  - source_labels: [device]
    regex: (.*)
    action: replace
    target_label: interface
    replacement: $1
```
Restart prometheus service:
```sh
systemctl restart prometheus
```

