Create/edit /etc/docker/daemon.json file:
```bash
vi /etc/docker/daemon.json
```
Add below given lines in it:
```yaml
{
  "metrics-addr" : "127.0.0.1:9323",
  "experimental" : true
}
```
Restart docker service:
```bash
systemctl restart docker
```
Verify if docker is exporting the metrics now:
```bash
curl localhost:9323/metrics
```
Created a job called docker in Prometheus

Edit /etc/prometheus/prometheus.yml file:
```bash
vi /etc/prometheus/prometheus.yml
```
Add below given lines under scrape_configs:
```yaml
  - job_name: "docker"
    static_configs:
      - targets: ["localhost:9323"]
```
Restart prometheus service:
```bash
systemctl restart prometheus
```
