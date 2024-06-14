# Prometheus Configuration Guide

This repository contains the configuration files for setting up Prometheus monitoring and alerting. Follow this guide to understand the configurations and make necessary changes according to your setup.

## Repository Structure
```
prometheus
│   README.md
└───config
        alert.rules.yml
        prometheus.yml

```



### Files Description

- `README.md`: This guide.
- `config/alert.rules.yml`: Contains the alerting rules for Prometheus.
- `config/prometheus.yml`: Prometheus configuration file, defining global settings, alerting, and scrape configurations.

## Prometheus Configuration (`prometheus.yml`)

The `prometheus.yml` file configures Prometheus server settings, including global parameters, alerting rules, and scrape jobs for monitoring targets.

### Global Configuration

```yaml
global:
  scrape_interval: 15s  # How frequently to scrape targets
  evaluation_interval: 15s  # How frequently to evaluate 
```
```
  rules
  external_labels:
  monitor: 'dev_monitor'  # External labels to add to all metrics
```

- `scrape_interval:` Interval between consecutive scrapes of targets.
- `evaluation_interval:` Interval between consecutive evaluations of rules.
- `external_labels:` Labels to add to any time series or alerts that this Prometheus instance produces.

### Alerting Configuration
```yaml
alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - "alertmanager:9093"  # Alertmanager service address in Docker Compose
```

- `alertmanagers:` List of Alertmanager instances to send alerts to.
- `targets:` Addresses of Alertmanager instances.

### Rule Files
```yaml
rule_files:
  - "/etc/prometheus/alert.rules.yml"  # Path to alert rules file
```
- `rule_files:` List of files from which Prometheus will load alerting rules.

## Scrape Configurations
Scrape configurations define which targets to scrape and how to scrape them.

### Remote Node Exporter
```yaml
- job_name: 'remote-node-exporter'
  static_configs:
    - targets:
        - '<REMOTE_IP_1>:9100'
        - '<REMOTE_IP_2>:9100'
        - '<REMOTE_IP_3>:9100'
        - '<REMOTE_IP_4>:9100'
        - '<REMOTE_IP_5>:9100'
        - '<REMOTE_IP_6>:9100'
```

- `job_name:` Name of the scrape job.
- `targets:` List of targets to scrape metrics from. Replace <REMOTE_IP_X> with your actual node exporter IP addresses and ports.

### Blackbox Exporter
```yaml
- job_name: 'blackbox'
  metrics_path: /probe
  params:
    module: [http_2xx]  # Expect an HTTP 200 response
  static_configs:
    - targets:
        - 'https://www.example1.com'
        - 'https://www.example2.com'
        - 'https://www.example3.com'
        - 'http://example4.com'
  relabel_configs:
    - source_labels: [__address__]
      target_label: __param_target
    - source_labels: [__param_target]
      target_label: instance
    - target_label: __address__
      replacement: "blackbox_exporter:9115"  # Blackbox exporter service address in Docker Compose
    - target_label: region
      replacement: "local"  # Set the region label to 'local'
```

- `job_name:` Name of the scrape job.
- `metrics_path:` Path for the probe metrics.
- `params:` Parameters for the probe, e.g., expecting an HTTP 200 response.
- `targets:` List of websites to monitor. Replace the example URLs with the websites you want to monitor.
- `relabel_configs:` Relabeling configurations to rewrite the labels.