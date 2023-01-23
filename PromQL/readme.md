### PromQL

### Selector, Matchers and Modifiers
```
up{job="web"}

process_cpu_seconds_total{job="Node Exporter"}
process_cpu_seconds_total{job!="Node Exporter"}

node_memory_MemAvailable_bytes{job="loadbalancer"}

prometheus_http_requests_total{handler=~"/api.*"}
prometheus_http_requests_total{handler!~"/api.*"}

node_cpu_seconds_total{job="web",cpu="0", mode=~"system|user"}

node_network_receive_bytes_total{instance="node02:9100", device!="lo"}

node_context_switches_total{instance="node02:9100"} @1654920221

node_memory_MemAvailable_bytes{instance="node01:9100"} offset 1h

node_context_switches_total{instance="node01:9100"} @1654920221 offset 30m
```
### Operators, Vector Mtching
```
prometheus_http_requests_total*2

node_cpu_seconds_total>300

node_filesystem_avail_bytes{job="web"} > 1000

prometheus_http_request_total and node_cpu_seconds_total
prometheus_http_request_total or node_cpu_seconds_total

node_network_receive_bytes_total{instance="loadbalancer:9100"} <= 10000

node_filesystem_files > 500000 and node_filesystem_files < 10000000

node_filesystem_avail_bytes{job="web", device!="tmpfs"}*100 / node_filesystem_size_bytes{job="web", device!="tmpfs"}

sum(node_filesystem_size_bytes{instance="loadbalancer:9100"})

count(node_cpu_seconds_total{instance="loadbalancer:9100", mode="idle"})

http_errors_total / ignoring(error) http_erequests_total

http_errors_total / on(instance, job, path) http_erequests_total

http_upload_failed_bytes_total*100 / ignoring(error) group_left http_uploaded_bytes_total
```
### Aggregation
```
http_requests{method="get", path="/auth", instance="node1"} 3
http_requests{method="post", path="/upload", instance="node2"} 8
http_requests{method="get", path="/tasks"}, instance="node1" 6

sum(http_requests)

max(http_requests)

avg(http_requests)

sum by(path) (http_requests)

sum by(method) (http_requests)

sum by(method, instance) (http_requests)

count by(instance) (http_requests{ method="get"})

sum without(path) (http_requests)

sum by(instance)(node_network_receive_bytes_total) / sum by(instance)(node_network_receive_packets_total)

node_cpu_seconds_total{mode="user"}*100 /ignoring(mode, job) sum by(instance, cpu) (node_cpu_seconds_total)
```
### Functions
```
time()

time() - process_start_time_seconds

rate(http_errors[1m])

node_cpu_seconds_total
ceil(node_cpu_seconds_total)
floor(node_cpu_seconds_total)
abs(1-node_cpu_seconds_total)

sort(node_cpu_seconds_total)
sort_desc(node_cpu_seconds_total)
```
### Date & Time Functions
```
Minute()         |{}07
Hour()           |{}15
Day_of_week()    |{}4
Day_of_month()   |{}22
Day_in_month()   |{}30
Month()          |{}09
Year()           |{}2022
```
### Subqueries
![image](https://user-images.githubusercontent.com/3519706/214003389-22a6ab2f-faa2-403d-a350-0f3bcad1d7a4.png)
```
<instant_query> [<range>:<resolutions>] [offset <duration>]

rate(http_requests_total[1m]) [5m:30s]
1m - sample range
5m - query range(get data from last 5 minutes)
30s - query step for subquery

max_over_time(node_filesystem_avail_bytes[10m])

max_over_time(rate(http_requests_total[1m])) [5m:30s]
```
### Histogram

To get the total sum of latency across all requests: 
```
requests_latency_seconds_sum
```
To get the rate of increase of the sum of latency across all requests:
```
rate(requests_latency_seconds_sum[1m])
```
To calculate the average latency of a request over the past 5m
```
rate(requests_latency_seconds_sum[5m]) / rate(requests_latency_seconds_count[5m])

histogram_quantile(0.75, request_latency_seconds_bucket)
{instance="192.168.1.66:8000", job="api", method="GET", path="/articles"} .076
```
