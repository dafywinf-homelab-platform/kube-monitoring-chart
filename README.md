# Kube Monitoring Stack

This Helm chart deploys
the [kube-prometheus-stack](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack),
which includes:

- Prometheus Operator
- Prometheus
- Alertmanager
- Grafana
- node-exporter
- kube-state-metrics

## Deployment (Argo CD)

This chart is deployed by **Argo CD**, not by hand. The Application lives in
[homelab-gitops](https://github.com/dafywinf-homelab-platform/homelab-gitops) under
`platform -> monitoring -> kube-monitoring`, tracking `main` of this repo with release name `kube-monitoring`,
namespace `kube-monitoring` and value files `values.yaml` + `environments/homelab.yaml`.

**Merging to `main` deploys.** Don't run `helm upgrade` against the cluster: Argo CD would revert it.

* `kube-prometheus-stack` is pinned to an exact version in `Chart.yaml`, with `Chart.lock` committed, so Argo CD renders
  exactly what was reviewed. Bump both together (`helm dependency update`).
* HTTPS is terminated by Traefik with its built-in default certificate. The chart ships no TLS secrets: the old
  self-signed certs were gitignored, so Argo CD could never have rendered them.

To preview changes locally:

```bash
helm dependency build
helm template kube-monitoring . -n kube-monitoring -f values.yaml -f environments/homelab.yaml
helm show values prometheus-community/kube-prometheus-stack   # all customisable values
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
