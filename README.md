# Observability stack: Prometheus, Grafana and Loki

A containerized observability stack run with Docker Compose. Prometheus and Node Exporter collect host metrics, Promtail ships Docker container logs to Loki, and Grafana is the single place to view both.

## Components

| Service | Image | Port | Role |
|---|---|---|---|
| Prometheus | prom/prometheus | 9090 | Scrapes metrics every 15 s (itself and Node Exporter) |
| Node Exporter | prom/node-exporter | internal only | Exposes host metrics |
| Loki | grafana/loki 2.9.4 | 3100 | Stores logs on the local filesystem |
| Promtail | grafana/promtail 2.9.4 | none | Reads Docker container logs and pushes them to Loki |
| Grafana | grafana/grafana | 3000 | Dashboards and log exploration |

All services share one Docker network (monitoring) and restart unless stopped. Grafana and Loki data are kept in named volumes.

## Repository layout

- docker-compose.yml: the five services, the network and the volumes
- prometheus/prometheus.yml: scrape jobs (prometheus, node)
- loki/loki-config.yml: single-node Loki, filesystem storage
- promtail/promtail-config.yml: reads /var/lib/docker/containers/*/*-json.log

## Run it

```bash
docker compose up -d
```

Then open Grafana at http://localhost:3000 and add two data sources:

- Prometheus: http://prometheus:9090
- Loki: http://loki:3100

To stop it: docker compose down (add -v to also delete the stored data).

## Incident exercise: host CPU saturation

The point of the stack is to see metrics and logs of the same moment side by side. The exercise I use to test it:

1. Baseline: open the Node Exporter Full dashboard in Grafana with the last 15 minutes selected. CPU is low and stable.
2. Trigger: on the host, outside any container, run yes > /dev/null for 60 to 90 seconds to saturate a CPU core.
3. Observe: the CPU panels in Grafana rise. In Explore, switch to the Loki data source and query {job="dockerlogs"}. The container logs keep flowing, so the system is under load but alive.
4. Stop: press Ctrl+C. CPU returns to the baseline.
5. Evidence: three screenshots, the CPU spike, CPU back to normal, and the Loki logs for the same timestamps.


## Notes and limits

- Grafana runs with its default admin login. Change it before exposing the port anywhere beyond localhost.
- Loki has authentication disabled and is meant for a local lab only.
- The prometheus and grafana images use the latest tag, so pin versions for anything repeatable.
