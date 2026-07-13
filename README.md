# Cross-Platform Host Monitoring

A monitoring pipeline for Linux and Windows hosts, built to understand how shell-collected signals bridge into a pull-based observability stack rather than treating the tools as black boxes.

## Status
🚧 In progress — Phase 0/1

## Architecture

- **node_exporter / windows_exporter** — expose standard OS metrics (CPU, memory, disk, network) natively on each host.
- **Bash / PowerShell collectors** — cover what the exporters don't: service and process health, written to `.prom` files and picked up by each exporter's textfile collector.
- **Prometheus** — scrapes both hosts, evaluates alert rules.
- **Alertmanager** — routes firing alerts to a webhook.
- **Incident snapshot** — triggered by the webhook, captures host state (processes, logs, sockets, disk) at the moment an alert fires.
- **Grafana** — dashboards over Prometheus.

## Phases

| Phase | Scope | Status |
|---|---|---|
| 0 | Repo scaffolding, environment setup | ✅ |
| 1 | Linux vertical slice: node_exporter → Prometheus → Grafana | 🚧 |
| 2 | Full Linux metric collection | ⬜ |
| 3 | Alerting rules and thresholds | ⬜ |
| 4 | Incident diagnostics (webhook + snapshot) | ⬜ |
| 5 | Windows host (windows_exporter) | ⬜ |
| 6 | docker-compose packaging | ⬜ |

## Design decisions

- Exporters own standard OS metrics; collector scripts only cover service/process health — no duplicating what node_exporter/windows_exporter already provide.
- Alerting via Prometheus rules + Alertmanager, not Grafana's built-in alerting.
- Incident snapshots are event-driven (webhook-triggered), not polled.
- Cross-network scrape path (local Linux VM ↔ Azure Windows VM) is an open decision, to be finalized in Phase 5.

## Local environment

- Linux: Multipass (Ubuntu) VM, run locally.
- Windows: Azure VM.
- Central stack (Prometheus/Grafana/Alertmanager): local for now, containerized in Phase 6.