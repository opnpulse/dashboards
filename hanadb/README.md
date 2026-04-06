# Grafana Dashboards

There are two dashboards to monitor SAP HANA Databases managed by KubeDB.

- KubeDB / HanaDB / Summary: Shows overall summary of a HanaDB instance.
- SAP HANA: Shows SAP HANA internal metrics for an instance.

Note: These dashboards are compatible with **Grafana 7.x**. The `hanadb_summary_dashboard.json` file was exported from Grafana **7.5.5**, and the `hanadb_dashboard.json` file was exported from Grafana **7.0.3**.

### Dependencies

HanaDB Dashboards are dependent on:

- [Prometheus Node Exporter](https://github.com/prometheus/node_exporter)
- [kube-state-metrics](https://github.com/kubernetes/kube-state-metrics)
- [Panopticon by Appscode](https://byte.builders/blog/post/introducing-panopticon/)
- HanaDB exporter metrics for the detailed SAP HANA dashboard

### Installation

#### 1. Install Prometheus Stack

Install Prometheus stack if you haven't done it already. You can use [kube-prometheus-stack](https://artifacthub.io/packages/helm/prometheus-community/kube-prometheus-stack) which installs the necessary components required for the HanaDB Grafana dashboards.

#### 2. Install Panopticon

Install Panopticon if you haven't done it already. Like other AppsCode products, [Panopticon](https://byte.builders/blog/post/introducing-panopticon/) also need a license to run.

**If you already have a license for KubeDB or Stash, you do not need to issue a new license for Panopticon. Your existing KubeDB or Stash license will work with Panopticon.**

Now, install Panopticon using the following commands:

```bash
helm upgrade -i monitoring-operator oci://ghcr.io/appscode-charts/monitoring-operator \
  --version v2024.9.30 \
  -n monitoring --create-namespace

helm upgrade -i panopticon oci://ghcr.io/appscode-charts/panopticon \
  --version v2024.9.30 \
  -n monitoring --create-namespace \
  --set-file license=/path/to/license-file.txt
```

#### 3. Add monitoring configuration in KubeDB managed HanaDB spec

To enable monitoring of a KubeDB HanaDB instance, you have to add monitoring configuration in the HanaDB CR spec like below:

```yaml
apiVersion: kubedb.com/v1alpha2
kind: HanaDB
metadata:
  name: sample-hanadb
  namespace: demo
spec:
  ...
  ...
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

#### Create DB Metrics Configurations

At first, you have to create a `MetricsConfiguration` object for DB. This `MetricsConfiguration` object is used by Panopticon to generate metrics for DB instances.

Install `kubedb-metrics` charts which will create the `MetricsConfiguration` object for DB:

```bash
helm upgrade -i kubedb-metrics oci://ghcr.io/appscode-charts/kubedb-metrics \
  --version v2024.9.30 \
  -n kubedb --create-namespace
```

#### Import Grafana Dashboard

Now, on your Grafana UI, import the json files of dashboards located in the `hanadb` folder of this repository.

1. Select `Import` from the `Plus(+)` icon from the left bar of the Grafana UI

![Import New Dashboard](/hanadb/images/import_dashboard_1.png)

2. Upload the json file and hit load button:

![Upload Dashboard JSON](/hanadb/images/import_dashboard_2.png)

If you followed the instruction properly, you should see the HanaDB Grafana dashboard in your Grafana UI.

### Samples

#### KubeDB / HanaDB / Summary

![KubeDB / HanaDB / Summary](/hanadb/images/hanadb-summary-1.png)
![KubeDB / HanaDB / Summary](/hanadb/images/hanadb-summary-2.png)
![KubeDB / HanaDB / Summary](/hanadb/images/hanadb-summary-3.png)
![KubeDB / HanaDB / Summary](/hanadb/images/hanadb-summary-4.png)

#### SAP HANA

![SAP HANA](/hanadb/images/hanadb-1.png)
![SAP HANA](/hanadb/images/hanadb-2.png)
![SAP HANA](/hanadb/images/hanadb-3.png)
![SAP HANA](/hanadb/images/hanadb-4.png)
![SAP HANA](/hanadb/images/hanadb-5.png)
![SAP HANA](/hanadb/images/hanadb-6.png)
![SAP HANA](/hanadb/images/hanadb-7.png)
