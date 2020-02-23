# Automation Dashboard [InfluxDB + Grafana]
> Dashboard to monitor live automation results using InfluxDB in Grafana

Create namespace; say, `monitoring`
```
kubectl create namespace monitoring
```
## **InfluxDB**

- By default, a database is created with name `dashboard`
- Also, the persistence volume is set true by default
```
cd influxdb/
helm install --name influxdb . --namespace monitoring

helm3 install influxdb . -n monitoring
```

#### Port forward InfluxDB service
```
kubectl port-forward --namespace monitoring $(kubectl get pods --namespace monitoring -l app=influxdb -o jsonpath='{ .items[0].metadata.ne }') 8086

kubectl --namespace monitoring port-forward $(kubectl get pods -n monitoring | grep 'influxdb' | awk '{print$1}') 8086
```
Prometheus access URL: http://127.0.0.1:8086/metrics

#### Manual entry [optional]
```
kubectl exec -i -t --namespace monitoring $(kubectl get pods --namespace monitoring -l app=influxdb -o jsonpath='{.items[0].metadata.name}') /bin/sh

influx
CREATE DATABASE dashboard
show databases
USE dashboard
SHOW SERIES
```
#### Post data using endpoint
```
curl -i -XPOST 'http://localhost:8086/write?db=dashboard' --data-binary 'country status=3'
```

## **Grafana**
```
cd grafana/
helm install --name grafana . --namespace monitoring

helm3 install grafana . -n monitoring
```

#### Access grafana service
By default, the credentials are defined in this chart
```
username: admin
password: admin
```
In-case if you remove password, you can retrieve it from,
```
kubectl get secret --namespace monitoring grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```
#### Port forward Grafana
```
kubectl --namespace monitoring port-forward $(kubectl get pods -n monitoring | grep 'grafana' | awk '{print$1}') 3000
```
Grafana access URL: http://localhost:3030/