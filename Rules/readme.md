### Recording Rules

Create a new file /etc/prometheus/node-rules.yaml:
```bash
vi /etc/prometheus/node-rules.yaml
```
Add below lines in it:
```yaml
groups:
  - name: node
    interval: 15s
    rules:
      - record: node_network_receive_bytes_rate
        expr: rate(node_network_receive_bytes_total{job="nodes"}[2m])
```
Edit /etc/prometheus/prometheus.yml file:
```bash
vi /etc/prometheus/prometheus.yml
```
Add below line under rule_files: section:
```yaml
  - "node-rules.yaml"
```
Restart the prometheus service:
```bash
systemctl restart prometheus
```
### Add additional rules

Edit /etc/prometheus/node-rules.yml file:
```bash
vi /etc/prometheus/node-rules.yml
```
Add the required rule so that the file looks like below:
```yaml
groups:
  - name: node
    interval: 15s
    rules:
      - record: node_network_receive_bytes_rate
        expr: rate(node_network_receive_bytes_total{job="nodes"}[2m])
      - record: node_network_receive_bytes_rate_avg
        expr: avg by(instance) (node_network_receive_bytes_rate)
      - record: node_filesystem_free_percent
        expr: 100 * node_filesystem_free_bytes{job="nodes"} / node_filesystem_size_bytes{job="nodes"}
```
Restart the prometheus service:
```bash
systemctl restart prometheus
```
### Add additional rules files

Create a new file /etc/prometheus/api-rules.yaml:
```bash
vi /etc/prometheus/api-rules.yaml
```
Add below lines in it:
```yaml
groups:
  - name: api
    interval: 15s
    rules:
      - record: avg_latency_2m
        expr: rate(http_request_total_sum{job="api"}[2m]) / rate(http_request_total_count{job="api"}[2m])
```
Edit /etc/prometheus/prometheus.yml file:
```bash
vi /etc/prometheus/prometheus.yml
```
Add below line under rule_files: section:
```yaml
  - "api-rules.yaml"
```
Restart prometheus service:
```bash
systemctl restart prometheus
```
### Use common configuration to all files

Edit /etc/prometheus/prometheus.yml file:
```bash
vi /etc/prometheus/prometheus.yml
```
Change rule_files: section so that it looks like below:
```yaml
rule_files:
  - "*rules.yaml"
```
Restart the prometheus service:
```bash
systemctl restart prometheus
```
