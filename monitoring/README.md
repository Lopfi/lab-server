Monitoring stack (Grafana + Prometheus + Loki)
=============================================

What this provides
- Prometheus: metrics scraping and storage (port 9090)
- Grafana: visualization (port 3000, admin/admin)
- Loki: log storage (port 3100)
- Promtail: tails /var/log and pushes to Loki
- Grafana Agent: optional collector (agent/agent-config.yml)
 - Loki: log storage (port 3100)
 - Alloy: replaces Promtail for logs collection (collects journal and files and forwards to Loki)
 - Grafana Agent: optional collector (agent/agent-config.yml)

Quick start
-----------
1. From `monitoring/` run (recommended - uses the working compose file that includes Alloy):

```bash
docker compose -f docker-compose.alloy.yml up -d
```

2. Open Grafana at http://<host>:3000 (admin/admin). Prometheus and Loki are auto-provisioned as datasources.

Notes and assumptions
---------------------
- You asked for "grafana alloy" — I interpreted this as Grafana Agent and included an optional `grafana-agent` service with a minimal config in `agent/agent-config.yml`.
- Prometheus in this compose scrapes exporters directly. If you prefer the Agent as the primary collector (remote_write), you'll need a remote write-compatible storage (Cortex/Mimir) — Prometheus itself does not accept remote_write as an ingestion endpoint.

Logs with Alloy (promtail deprecation)
------------------------------------
Promtail has been replaced in this stack by Grafana Alloy. Alloy runs as the central log collector in the compose and is configured at `monitoring/alloy/alloy-config.alloy`.

Default behavior in this repo:
- Alloy tails `/var/log/*log` on the host and reads the systemd journal. These are forwarded to Loki at `http://loki:3100/loki/api/v1/push`.
- The Alloy UI/debug port is exposed at 12345 for pipeline inspection and testing.

Deployment choices for logs:
- Centralized: run a single Alloy instance (this compose) and forward remote hosts' logs to it (syslog/GELF).
- Per-host: run Alloy on each host and forward to central Loki. This is more secure and scalable for multi-host fleets.

Adding Proxmox monitoring
-------------------------
1. Install a Proxmox exporter on your Proxmox host. A common option is `pve-exporter` or `prometheus-pve-exporter` which typically listens on port 9221.
2. In `prometheus/prometheus.yml`, add or update the `proxmox` job's `static_configs` targets with your Proxmox host IP and port, e.g.:

```yaml
- job_name: 'proxmox'
  static_configs:
    - targets: ['192.168.1.10:9221']
      labels:
        role: proxmox
```

3. Reload Prometheus (docker restart prometheus) or use the HTTP reload API if enabled.
4. In Grafana, import a Proxmox dashboard (search the Grafana dashboards site for a community dashboard) or build visualizations using `role="proxmox"` node labels.

Adding TrueNAS monitoring
-------------------------
TrueNAS CORE and SCALE have different options:

- TrueNAS SCALE: can run Prometheus exporters directly in apps or use `node_exporter`/`telegraf`.
- TrueNAS CORE: you might run `node_exporter` externally or use the built-in SNMP/REST and scrape with Telegraf.

Example using node_exporter on TrueNAS (if available):

1. Run `node_exporter` on TrueNAS listening on 9100.
2. Add to `prometheus/prometheus.yml` under the `truenas` job:

```yaml
- job_name: 'truenas'
  static_configs:
    - targets: ['192.168.1.11:9100']
      labels:
        role: truenas
```

3. Restart Prometheus and create Grafana dashboards using these metrics.

Tips and next steps
-------------------
- For dynamic fleets, use `file_sd` or Consul/Consul-Discovery to keep target lists updated.
- If you want the Grafana Agent to be the central collector and to store metrics remotely, consider deploying Cortex/Mimir and configure agent `remote_write` to it.
- Secure Grafana and Loki with proper authentication and TLS before exposing them publicly.
- Add dashboards under `grafana/provisioning/dashboards` and update provisioning to auto-import JSON dashboard files.

If you want, I can:
- Add example exporters' Docker Compose snippets for Proxmox/TrueNAS if you prefer running exporters as containers.
- Generate a few Grafana dashboard JSON files and add provisioning to auto-import them.

Verification
------------
After bringing the stack up, verify:
- Prometheus: http://<host>:9090/targets shows your scrape targets.
- Grafana: check datasources under Configuration -> Data Sources.
- Loki: verify promtail logs are visible in Explore -> Logs in Grafana.
 - Loki: verify logs from Alloy are visible in Explore -> Logs in Grafana.

Change log
----------
- Created docker-compose and minimal configs for Prometheus, Grafana, Loki, Promtail, and an optional Grafana Agent config.



  - job_name: 'node_exporter'
    # Replace HOST_IP with the IP(s) of your hosts (Proxmox, TrueNAS, other servers)
    static_configs:
      - targets: ['HOST_IP:9100']
        labels:
          role: node

  - job_name: 'proxmox'
    # If you run a Proxmox exporter (for example pve_exporter), put its host:port here
    static_configs:
      - targets: ['PROXMOX_HOST:9221']
        labels:
          role: proxmox

  - job_name: 'truenas'
    # TrueNAS SCALE/CORE may expose exporters or you can run node_exporter/telegraf on it
    static_configs:
      - targets: ['TRUENAS_HOST:9100']
        labels:
          role: truenas