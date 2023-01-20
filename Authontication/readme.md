
SSH to node01
```sh
ssh root@node01
```
Create the config:
```sh
mkdir /etc/node_exporter/
touch /etc/node_exporter/config.yml
chmod 700 /etc/node_exporter
chmod 600 /etc/node_exporter/config.yml
chown -R nodeusr:nodeusr /etc/node_exporter
```
Edit node_exporter service
```sh
vi /etc/systemd/system/node_exporter.service
```
Change below line:
```sh
ExecStart=/usr/local/bin/node_exporter
```
to
```sh
ExecStart=/usr/local/bin/node_exporter --web.config=/etc/node_exporter/config.yml
```
Reload the daemon and Restart node_exporter service
```sh
systemctl daemon-reload
systemctl restart node_exporter
exit
```
SSH to node01:
```sh
ssh root@node01
```
Install apache2-utils package:
```sh
apt update
apt install apache2-utils -y
```
Generate password hash:
```sh
htpasswd -nBC 10 "" | tr -d ':\n'; echo
```
It will ask for the password twice as below (enter password secret-password twice):
```sh
New password: 
Re-type new password: 
```
Finally, you will get a hashed value of your password.

Edit /etc/node_exporter/config.yml file:
```sh
vi /etc/node_exporter/config.yml
```
Add below lines in it:
```yaml
basic_auth_users:
  prometheus: <hashed-password>
```
Restart node_exporter service
```sh
systemctl restart node_exporter
exit
```
You can verify the changes using curl command:
```sh
curl http://node01:9100/metrics
```
return output should be Unauthorized

Try using below given commands:
```sh
curl -u prometheus:secret-password http://node01:9100/metrics
curl -u prometheus:secret-password http://node02:9100/metrics
```
Edit the Prometheus configuration file
```sh
vi /etc/prometheus/prometheus.yml
```
Under - job_name: "nodes" add below lines:
```yaml
basic_auth:
  username: prometheus
  password: secret-password
```
Restart prometheus service:
```sh
systemctl restart prometheus
```
### SSL
SSH to node01
```sh
ssh root@node01
```
Generate the certificate and key
```sh
openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout node_exporter.key -out node_exporter.crt -subj "/C=US/ST=California/L=Oakland/O=MyOrg/CN=localhost" -addext "subjectAltName = DNS:localhost"
```
Move the crt and key file under /etc/node_exporter/ directory
```sh
mv node_exporter.crt node_exporter.key /etc/node_exporter/
```
Change ownership:
```sh
chown nodeusr.nodeusr /etc/node_exporter/node_exporter.key
chown nodeusr.nodeusr /etc/node_exporter/node_exporter.crt
```
Edit /etc/node_exporter/config.yml file:
```sh
vi /etc/node_exporter/config.yml
```
Add below lines in this file:
```yaml
tls_server_config:
  cert_file: node_exporter.crt
  key_file: node_exporter.key
```
Restart node exporter service:
```sh
systemctl restart node_exporter
exit
```
You can verify your changes using curl command:
```sh
curl -u prometheus:secret-password -k https://node01:9100/metrics
```
Copy the certificate from node01 to Prometheus server
```sh
scp root@node01:/etc/node_exporter/node_exporter.crt /etc/prometheus/node_exporter.crt
```
Change certificate file ownership:
```sh
chown prometheus.prometheus /etc/prometheus/node_exporter.crt
```
Edit /etc/prometheus/prometheus.yml file
```sh
vi /etc/prometheus/prometheus.yml 
```
Add below given lines under - job_name: "nodes"
```yaml
    scheme: https
    tls_config:
      ca_file: /etc/prometheus/node_exporter.crt
      insecure_skip_verify: true
```
Restart prometheus service
```sh
systemctl restart prometheus
```
