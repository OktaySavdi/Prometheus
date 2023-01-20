# Install Prometheus 

Add Prometheus user as below:
```sh
useradd --no-create-home --shell /bin/false prometheus
```
Create Directories for storing prometheus config file and data:
```sh
mkdir /etc/prometheus
mkdir /var/lib/prometheus
```
Change the permissions:
```sh
chown prometheus:prometheus /etc/prometheus
chown prometheus:prometheus /var/lib/prometheus
```
Copy the binaries:
```sh
cp /root/prometheus-2.40.1.linux-amd64/prometheus /usr/local/bin/
cp /root/prometheus-2.40.1.linux-amd64/promtool /usr/local/bin/
```
Change the ownership of binaries:
```sh
chown prometheus:prometheus /usr/local/bin/prometheus
chown prometheus:prometheus /usr/local/bin/promtool
```
Copy the directories consoles and console_libraries:
```sh
cp -r /root/prometheus-2.40.1.linux-amd64/consoles /etc/prometheus
cp -r /root/prometheus-2.40.1.linux-amd64/console_libraries /etc/prometheus
```
Change the ownership of directories consoles and console_libraries:
```sh
chown -R prometheus:prometheus /etc/prometheus/consoles
chown -R prometheus:prometheus /etc/prometheus/console_libraries
```
Move prometheus.yml file to /etc/prometheus directory:
```sh
cp /root/prometheus-2.40.1.linux-amd64/prometheus.yml /etc/prometheus/prometheus.yml
```
Change the ownership of file /etc/prometheus/prometheus.yml:
```sh
chown prometheus:prometheus /etc/prometheus/prometheus.yml
```
Create a service for prometheus:
```sh
vi /etc/systemd/system/prometheus.service
```
Add below lines in it:
```sh
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
    --config.file /etc/prometheus/prometheus.yml \
    --storage.tsdb.path /var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```
Run below commands:
```sh
systemctl daemon-reload
systemctl start prometheus
systemctl enable prometheus
systemctl status prometheus
```
