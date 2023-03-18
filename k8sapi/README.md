# k8sapi

The `k8sapi` module collects Kubernetes API server metrics and forwards them
to a Prometheus-compatible Grafana Agent Flow component.

> **NOTE**: `k8sapi` must be used with a module loader which can pass arguments
> to loaded modules, such as `module.git`.

## Usage

```river
module.git "k8sapi" {
  repository = "https://github.com/rfratto/agent-modules.git"
  revision   = "main"
  path       = "k8sapi/module.river"

  arguments {
    forward_metrics_to = PROMETHEUS_RECEIVER_LIST
  }
}
```

## Module arguments

The following arguments are supported when passing arguments to the module
loader:

| Name | Type | Description | Default | Required
| ---- | ---- | ----------- | ------- | --------
| `forward_metrics_to` | `list(MetricsReceiver)` | Receivers to forward collected metrics to. | | yes
| `scrape_interval` | `duration` | How often to collect metrics. | `"60s"` | no
| `scrape_timeout` | `duration` | Timeout period for collecting metrics. | `"10s"` | no
| `kubeconfig_file` | `string` | Path to the Kubernetes config file. | `""` | no

`k8sapi` uses in-cluster authentication for connecting to Kubernetes, and
expects to be running inside the Kubernetes cluster.

## Module exports

`k8sapi` does not export any fields.

## Example

This example uses the `module.git` loader to run the module and forward metrics
to a [`prometheus.remote_write` component][prometheus.remote_write]:

```river
module.git "k8sapi" {
  repository = "https://github.com/rfratto/agent-modules.git"
  revision   = "main"
  path       = "k8sapi/module.river"

  arguments {
    forward_metrics_to = [prometheus.remote_write.default.receiver]
  }
}

prometheus.remote_write "default" {
  endpoint {
    url = env("PROMETHEUS_URL")
  }
}
```

[prometheus.remote_write]: https://grafana.com/docs/agent/latest/flow/reference/components/prometheus.remote_write