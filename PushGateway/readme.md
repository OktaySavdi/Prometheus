### Installation

Download and install the Pushgateway binary:
```sh
wget https://github.com/prometheus/pushgateway/releases/download/v1.5.0/pushgateway-1.5.0.linux-amd64.tar.gz
tar xvfz pushgateway-1.5.0.linux-amd64.tar.gz
cp pushgateway-1.5.0.linux-amd64/pushgateway /usr/local/bin/
useradd -M -r -s /bin/false pushgateway
chown pushgateway:pushgateway /usr/local/bin/pushgateway
```
Create a systemd unit file for Pushgateway:
```sh
vi /etc/systemd/system/pushgateway.service
```
Add below code in it:
```sh
[Unit]
Description=Prometheus Pushgateway
Wants=network-online.target
After=network-online.target

[Service]
User=pushgateway
Group=pushgateway
Type=simple
ExecStart=/usr/local/bin/pushgateway

[Install]
WantedBy=multi-user.target
```
Start and enable the pushgateway service:
```sh
systemctl enable pushgateway
systemctl start pushgateway
```
Edit /etc/prometheus/prometheus.yml file:
```sh
vi /etc/prometheus/prometheus.yml
```
Add below lines under scrape_configs:
```yaml
  - job_name: pushgateway
    honor_labels: true
    static_configs:
      - targets: ["localhost:9091"]
```
Restart the prometheus service:
```sh
systemctl restart prometheus
```
Run the below commands:
```sh
cat <<EOF | curl --data-binary @- http://localhost:9091/metrics/job/video_processing/instance/mp4_node1
# TYPE processing_time_seconds gauge
processing_time_seconds{quality="hd"} 120
# TYPE processed_videos_total gauge
processed_videos_total{quality="hd"} 10
# TYPE processed_bytes_total gauge
processed_bytes_total{quality="hd"} 4400
EOF
```
```sh
cat <<EOF | curl --data-binary @- http://localhost:9091/metrics/job/video_processing/instance/mov_node1
# TYPE processing_time_seconds gauge
processing_time_seconds{quality="hd"} 400
# TYPE processed_videos_total gauge
processed_videos_total{quality="hd"} 250
# TYPE processed_bytes_total gauge
processed_bytes_total{quality="hd"} 96000
EOF
```

Send HTTP POST request using the following URL :
```
http://<pushgateway_address>:<port>/metrics/job/<job_name>/<label1>/<value1>/<label2>/<value2>
```

`<job_name>` will be the job label of the metrics pushed
`<label>/<value>` in the url path is used as a grouping key – allows for grouping
metrics together to update/delete multiple metrics at once
```
echo "example_metric 4421" | curl --data-binary @-
http://localhost:9091/metrics/job/db_backup
```
Metric data has to be passed in as binary data with the `–data-binary` flag
`@-` tells curl to read the binary data from std-in which is the output of the

```bash
cat <<EOF | curl --data-binary @-
http://localhost:9091/metrics/job/job1/instance/instance1
# TYPE my_job_duration gauge
my_job_duration{label="val1"} 42
# TYPE another_metric counter
# HELP another_metric Just an example.
another_metric 12
EOF
```
### Grouping
```
http://<pushgateway_address>:<port>/metrics/job/<job_name>/<label1>/<value1>/<label2>/<value2>
```
The URL path(job_name + labels) acts as a `grouping` key

All metrics sent to a specific URL path are part of a “group”

Groups allow you to update/delete multiple metrics at once

![image](https://user-images.githubusercontent.com/3519706/214237452-7dd53481-e2a7-4eae-8e15-d65c1418a72d.png)

![image](https://user-images.githubusercontent.com/3519706/214237494-e5900862-fc8b-462b-be7c-1e26dcadeb86.png)

![image](https://user-images.githubusercontent.com/3519706/214237548-d4654e49-3cf3-4873-b8ab-446125a05a98.png)

![image](https://user-images.githubusercontent.com/3519706/214237603-aed77650-1e7b-4d22-9da0-74a7837d7cc2.png)




