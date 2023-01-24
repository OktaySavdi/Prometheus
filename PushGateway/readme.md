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




