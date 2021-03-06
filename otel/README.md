## OpenTelemetry Collector

## Overview

The OpenTelemetry Collector is a vendor-agnostic agent process that, via the Datadog exporter, exports telemetry data directly to Datadog servers. 
It reports metrics and traces from instrumented applications and general system metrics.

Host metrics will be shown in the default dashboard, but you can send arbitrary metrics to Datadog using the OpenTelemetry Collector. All metrics reported by the Collector will be namespaced under `otel.` to prevent collisions with metrics from other Datadog integrations.

## Setup

### Installation

Follow the [OpenTelemetry Collector documentation][2] to install the `opentelemetry-collector-contrib` distribution, or any other distribution that includes the Datadog exporter.

The Datadog Agent is **not** needed to export telemetry data to Datadog.

### Configuration

To export telemetry data to Datadog from the OpenTelemetry Collector, add the Datadog exporter to your metrics and traces pipelines.
The only required setting is [your API key][3].


A minimal configuration file to retrieve system metrics is as follows.

``` yaml
receivers:
  hostmetrics:
    scrapers:
      load:
      cpu:
      disk:
      filesystem:
      memory:
      network:
      paging:
      process:

processors:
  batch:
    timeout: 10s

exporters:
  datadog:
    api:
      key: "<Your API key goes here>"
      
service:
  pipelines:
    metrics:
      receivers: [hostmetrics]
      processors: [batch]
      exporters: [datadog]
```

For further information on the Datadog exporter settings and how to configure the pipeline, visit the [Datadog exporter for OpenTelemetry Collector documentation][4].


### Validation

Check the OpenTelemetry Collector logs to see the Datadog exporter being enabled and started correctly.
For example, with the configuration above you should find logging messages similar to the following.

``` 
Exporter is enabled.	{"component_kind": "exporter", "exporter": "datadog"}
Exporter is starting...	{"component_kind": "exporter", "component_type": "datadog", "component_name": "datadog"}
Exporter started.	{"component_kind": "exporter", "component_type": "datadog", "component_name": "datadog"}
Everything is ready. Begin running and processing data.
```

## Data Collected

### Metrics

The following metrics are provided by this check, if you're using the `hostmetrics` receiver as in the sample configuration above. You can send arbitrary metrics with other OpenTelemetry Collector components.

Different groups of metrics can be enabled and customized by following the [hostmetrics receiver instructions][7].
CPU and disk metrics are not available on macOS.

See [metadata.csv][6] for a list of metrics provided by this integration.

### Service Checks

The OpenTelemetry Collector does not include any service checks.

### Events

The OpenTelemetry Collector does not include any events.

## Troubleshooting

Need help? Contact [Datadog support][1].

[1]: https://docs.datadoghq.com/help/
[2]: https://opentelemetry.io/docs/collector/getting-started/
[3]: https://app.datadoghq.com/account/settings#api
[4]: https://docs.datadoghq.com/tracing/setup_overview/open_standards/#opentelemetry-collector-datadog-exporter
[5]: https://opentelemetry.io/docs/collector/getting-started/
[6]: https://github.com/DataDog/integrations-core/blob/master/opentelemetry/metadata.csv
[7]: https://github.com/open-telemetry/opentelemetry-collector/tree/master/receiver/hostmetricsreceiver
