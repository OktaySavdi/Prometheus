Download Prometheus AlertManager:
```sh
wget https://github.com/prometheus/alertmanager/releases/download/v0.21.0/alertmanager-0.21.0.linux-amd64.tar.gz
```
Extract the files
```sh
tar xzf alertmanager-0.21.0.linux-amd64.tar.gz
```
Create a Prometheus user, required directories, and make prometheus user as the owner of those directories:
```sh
groupadd -f alertmanager
useradd -g alertmanager --no-create-home --shell /bin/false alertmanager
mkdir -p /etc/alertmanager/templates
mkdir /var/lib/alertmanager
chown alertmanager:alertmanager /etc/alertmanager
chown alertmanager:alertmanager /var/lib/alertmanager
```
Move the downloaded Prometheus AlertManager binary:
```sh
mv alertmanager-0.21.0.linux-amd64 alertmanager-files
```
Install Prometheus AlertManager:
```sh
cp alertmanager-files/alertmanager /usr/bin/
cp alertmanager-files/amtool /usr/bin/
chown alertmanager:alertmanager /usr/bin/alertmanager
chown alertmanager:alertmanager /usr/bin/amtool
```
Install Prometheus AlertManager Configuration File:
```sh
cp alertmanager-files/alertmanager.yml /etc/alertmanager/alertmanager.yml
chown alertmanager:alertmanager /etc/alertmanager/alertmanager.yml
```
Setup Prometheus AlertManager Service:
```sh
vi /usr/lib/systemd/system/alertmanager.service
```
Add the following configuration and save the file:
```sh
[Unit]
Description=AlertManager
Wants=network-online.target
After=network-online.target

[Service]
User=alertmanager
Group=alertmanager
Type=simple
ExecStart=/usr/bin/alertmanager \
    --config.file /etc/alertmanager/alertmanager.yml \
    --storage.path /var/lib/alertmanager/

[Install]
WantedBy=multi-user.target
```
Update permissions:
```sh
chmod 664 /usr/lib/systemd/system/alertmanager.service
```
Reload systemd and Register Prometheus AlertManager:
```sh
systemctl daemon-reload
systemctl start alertmanager
```
Check the alertmanager service status using the following command.
```sh
systemctl status alertmanager
```
Create rules.yaml file in under /etc/prometheus/ directory:
```sh
vi /etc/prometheus/rules.yaml
```
Add below lines in it:
```yaml
groups:
  - name: node
    rules:
      - alert: LowDiskSpace
        expr: 100 * node_filesystem_free_bytes{job="nodes"} / node_filesystem_size_bytes{job="nodes"} < 10
        labels:
          severity: warning
          environment: prod
```
Update the /etc/prometheus/prometheus.yml config file:
```sh
vi /etc/prometheus/prometheus.yml
```
Add below line under rule_files: so that it looks like as below:
```yaml
rule_files:
  - "/etc/prometheus/rules.yaml"
```
Restart prometheus service:
```sh
systemctl restart prometheus
```
Edit /etc/prometheus/rules.yaml file:
```sh
vi /etc/prometheus/rules.yaml
```
Add below lines for NodeDown alert:
```yaml
annotations:
  message: "node {{.Labels.instance}} is down"
```
Restart prometheus service:
```sh
systemctl restart prometheus
```
