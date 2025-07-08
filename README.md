
# Kube Monitoring Stack

This Helm chart deploys the [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack),
which includes:

- Prometheus Operator
- Prometheus
- Alertmanager
- Grafana
- node-exporter
- kube-state-metrics

## Installation

Add the repo (if not already done):

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm search repo prometheus-community/kube-prometheus-stack
````

Creating the certificates:

```bash
# Prometheus
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -subj "/CN=prometheus.192-168-86-10.sslip.io" \
  -keyout certs/prometheus.key \
  -out certs/prometheus.crt

# Grafana
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -subj "/CN=grafana.192-168-86-10.sslip.io" \
  -keyout certs/grafana.key \
  -out certs/grafana.crt

# Alertmanager
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -subj "/CN=alertmanager.192-168-86-10.sslip.io" \
  -keyout certs/alertmanager.key \
  -out certs/alertmanager.crt
```

Configuring the chart:

Run the following command to view values that can be customised:

```bash
helm show values prometheus-community/kube-prometheus-stack
```

## Deploying the Stack

```bash
helm dependency build

helm upgrade --install kube-monitoring ./ \
  --namespace kube-monitoring \
  --create-namespace \
  --values values.yaml

# Confirm deployment successful
kubectl get all -n kube-monitoring
```

## (Optional) Uninstall Deployment

```bash
helm uninstall kube-monitoring --namespace kube-monitoring
```

## Accessing Grafana

```bash
https://prometheus.192-168-86-10.sslip.io
https://grafana.192-168-86-10.sslip.io
https://alertmanager.192-168-86-10.sslip.io
```

Then open [http://localhost:3000](http://localhost:3000)
Login: `admin` / `admin` (default)

## Notes

* The chart installs Grafana dashboards and Prometheus alerts out-of-the-box.
* You can add custom dashboards, alerts, or exporters using ServiceMonitor or PrometheusRule resources.

## Next Steps
* Integrate with the Grafana Operator if you prefer declarative dashboard management
