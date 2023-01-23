### HTTP API
```
curl <prometheus>:9090/api/v1/query --data 'query=node_arp_entries{instance="10.10.10.10:9100"}' | jq

curl <prometheus>:9090/api/v1/query --data 'query=node_arp_entries{instance="10.10.10.10:9100"}'

curl <prometheus>:9090/api/v1/query --data 'query=node_arp_entries{instance="10.10.10.10:9100"}' --data 'time=160636566.132'

curl <prometheus>:9090/api/v1/query --data 'query=node_arp_entries{instance="10.10.10.10:9100"}[5m]' --data 'time=160636566.132'
```
![image](https://user-images.githubusercontent.com/3519706/214004380-019b59fd-2477-425e-b0b3-97c4991595fa.png)
