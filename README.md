# Prometheus-stack

A Docker Compose monitoring stack: **Prometheus + Alertmanager + Grafana**, with exporters for hosts, containers,
network gear (SNMP), VMware and HP MSA storage.

It started from [Brian Christner's Docker Prometheus stack](https://github.com/vegasbrianc/prometheus) and was
customised for infrastructure monitoring (firewalls over SNMP, ESXi/vCenter, storage arrays).

> **Resumen:** stack de monitoreo con Docker Compose (Prometheus, Alertmanager, Grafana) y exporters de SNMP,
> VMware y HP MSA. Antes de usarlo, cambiá las credenciales de ejemplo (ver *Security*).

## What is inside

| Service | Image | Port | Purpose |
|---|---|---|---|
| `prometheus` | `prom/prometheus:v2.1.0` | 9090 | Metrics storage and alert rules (`prometheus/alert.rules`) |
| `alertmanager` | `prom/alertmanager` | 9093 | Alert routing, sends to Slack (`alertmanager/config.yml`) |
| `grafana` | `grafana/grafana` | 3000 | Dashboards, provisioned from `grafana/provisioning/` |
| `node-exporter` | `prom/node-exporter` | 9100 | Host metrics |
| `cadvisor` | `google/cadvisor` | 8080 | Container metrics |
| `snmp-exporter` | `quay.io/prometheus/snmp-exporter` | 9116 | SNMP devices, config in `snmp_exporter/` |
| `vmware_exporter` | `pryorda/vmware_exporter` | 9275 | vCenter / ESXi metrics, config in `vmware_exporter/` |

Other folders:

- `dashboards/`: Grafana dashboards (`Grafana_Dashboard.json`, `Grafana_Dashboard_prom_2.json`, `System_Monitoring.json`).
- `hp-msa-exporter/`: an exporter for HP MSA storage arrays (see *Credits*).
- `docker-stack.yml`: the same stack for Docker Swarm.
- The `*.json` dashboards in the root are earlier exports.

## Run it

```bash
docker-compose up -d
```

Then open Grafana at `http://localhost:3000` and Prometheus at `http://localhost:9090`.

## Security: change these before you use it

The repository ships **placeholders**. Replace them with your own values, and keep real secrets out of Git
(use an `.env` file or Docker secrets, and add it to `.gitignore`):

| File | What to set |
|---|---|
| `grafana/config.monitoring` | `GF_SECURITY_ADMIN_PASSWORD` (the value here is `change-me`). Consider `GF_USERS_ALLOW_SIGN_UP=false`. |
| `alertmanager/config.yml` | `api_url`: your Slack incoming-webhook URL. |
| `vmware_exporter/vmware_exporter/config.yml`, `esx.yml` | `vsphere_host`, `vsphere_user`, `vsphere_password`. Use a read-only account. |

Earlier versions of this repository had real values in these files. They are still in the Git history, so any
credential that was ever used with them must be treated as exposed and **rotated**.

## Credits and licences

- Stack layout: [vegasbrianc/prometheus](https://github.com/vegasbrianc/prometheus).
- `snmp_exporter/`: copy of [prometheus/snmp_exporter](https://github.com/prometheus/snmp_exporter), Apache-2.0.
- `vmware_exporter/`: copy of [pryorda/vmware_exporter](https://github.com/pryorda/vmware_exporter), BSD-3-Clause.
- `hp-msa-exporter/`: HPMSA Exporter. No licence file was included in the copy kept here; check the upstream project
  before reusing it.

The files that are not from those projects are my own configuration and dashboards, released under the MIT licence (see `LICENSE`). The copied projects keep their own licences.
