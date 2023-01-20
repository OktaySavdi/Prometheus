# install nodeexporter
```sh
ssh node01
wget https://github.com/prometheus/node_exporter/releases/download/v1.4.0/node_exporter-1.4.0.linux-amd64.tar.gz
tar xvf node_exporter-1.4.0.linux-amd64.tar.gz
cd node_exporter-1.4.0.linux-amd64
./node_exporter > /dev/null 2>&1 &
```
Modify the /etc/prometheus/prometheus.yml file:
```sh
vi /etc/prometheus/prometheus.yml
```
Add below code under scrape_configs::
```yaml
  - job_name: "nodes"
    static_configs:
      - targets: ['node01:9100', 'node02:9100']
```
Restart the process with below command:
```sh
systemctl restart prometheus
```
