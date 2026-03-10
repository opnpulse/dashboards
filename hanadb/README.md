# Grafana Dashboards

There are two dashboards to monitor SAP HANA databases managed by KubeDB.

- KubeDB / HanaDB / Summary: Shows overall summary of a HanaDB instance.
- SAP HANA: Shows SAP HANA internal metrics for an instance.

Note: These dashboards are developed in **Grafana version 7.5.5**

### Dependencies

HanaDB dashboards depend on:

- [Prometheus Node Exporter](https://github.com/prometheus/node_exporter)
- [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics)
- [Panopticon by AppsCode](https://byte.builders/blog/post/introducing-panopticon/) for the summary dashboard
- HANA exporter metrics for the detailed SAP HANA dashboard

### Installation

#### 1. Install Prometheus Stack

Install Prometheus stack if you haven't done it already. You can use [kube-prometheus-stack](https://artifacthub.io/packages/helm/prometheus-community/kube-prometheus-stack), which installs the components required for the HanaDB dashboards.

#### 2. Install Panopticon

Install Panopticon if you haven't done it already. The summary dashboard expects the KubeDB `kubedb_com_hanadb_*` metrics generated from a `MetricsConfiguration`.

#### 3. Add monitoring configuration in HanaDB spec

To enable exporter-based monitoring of a KubeDB HanaDB instance, add monitoring configuration in the HanaDB CR:

```yaml
apiVersion: kubedb.com/v1alpha2
kind: HanaDB
metadata:
  name: sample-hanadb
  namespace: demo
spec:
  monitor:
    agent: prometheus.io/operator
    prometheus:
      exporter:
        port: 9668
      serviceMonitor:
        labels:
          release: <helm-release-name-of-kube-prometheus-stack>
        interval: 10s
```

### Using Dashboards

Import the JSON files from the `hanadb` folder of this repository into Grafana.

- `hanadb_summary_dashboard.json`
- `hanadb_dashboard.json`
