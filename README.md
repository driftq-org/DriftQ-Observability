# DriftQ-Observability

Prometheus + Grafana stack for **DriftQ**. Ships with:
- Prometheus scrape config for the broker at `:8080/metrics`
- Grafana preprovisioned with a Prometheus datasource
- A starter **DriftQ Overview** dashboard
- Basic alert rules (broker down, high lag, high consumer latency)
- Optional OpenTelemetry Collector (OTLP) for future traces/metrics

> Works standalone. When `driftqd` is not running, the dashboard will just show no data.

---

## Quick start

```bash
git clone https://github.com/driftq-org/DriftQ-Observability.git
cd driftq-observability
docker compose up -d
```

Open Grafana: http://localhost:3000 (user: `admin`, pass: `admin`).
Prometheus: http://localhost:9090

### Point Prometheus at your broker
By default, Prometheus scrapes `host.docker.internal:8080`. If your broker runs elsewhere, edit **prometheus/prometheus.yml**:

```yaml
- job_name: "driftqd"
  static_configs:
    - targets: ["<your-host>:8080"]
```

Restart:
```bash
docker compose restart prometheus
```

---

## Files

```
grafana/
  provisioning/{datasources,dashboards}
  dashboards/driftq-overview.json
prometheus/
  prometheus.yml
  rules/driftq_rules.yml
otel-collector/config.yaml
docker-compose.yml
```

---

## Alerts
- **DriftQBrokerDown** – Prometheus cannot scrape the broker.
- **DriftQHighLag** – `max(driftq_partition_lag) > 10k` for 5m.
- **DriftQHighConsumerLatency** – `max(driftq_consumer_latency_ms) > 5s` for 5m.

Wire alertmanager later by adding an `alerting` section in Prometheus config.

---

## Customize

- Add/rename metrics in your broker; update PromQL in the dashboard JSON.
- Add tracing by pointing your apps to OTLP (`:4317` gRPC / `:4318` HTTP).

---

## License
Apache 2.0 – see `LICENSE`.
