# Observability Stack (Prometheus + Grafana + Loki)

This project demonstrates a complete **containerized observability stack** using Docker Compose.

It collects:
- **System metrics** using Prometheus + Node Exporter
- **Logs** using Loki + Promtail
- **Visualizes & correlates metrics and logs** using Grafana

---

## Architecture

- **Prometheus**: Scrapes system metrics
- **Node Exporter**: Exposes host metrics
- **Loki**: Stores logs
- **Promtail**: Ships Docker/container logs to Loki
- **Grafana**: Visualizes metrics and logs

All components run as Docker containers.

---

## Services & Ports

| Service        | Port |
|---------------|------|
| Grafana       | 3000 |
| Prometheus    | 9090 |
| Loki          | 3100 |
| Node Exporter | 9100 |

---

## How to Run

```bash
docker compose up -d
