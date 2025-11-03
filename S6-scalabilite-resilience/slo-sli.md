# Fiche SLO / SLI — Service API

| Service | SLI                  | SLO cible         | Fenêtre | Méthode de mesure                         | Budget d’erreur |
|---------|----------------------|-------------------|---------|-------------------------------------------|-----------------|
| API     | p95 latency          | < 300 ms          | 30 j    | Prometheus `histogram_quantile`          | 0,5 %           |
| API     | Error rate           | < 1 %             | 30 j    | `rate(http_requests_total{status=~"5.."})` / `rate(http_requests_total)` | 1 %             |
| API     | Availability         | ≥ 99,5 %          | 30 j    | Probe d’uptime / métriques Prometheus     | 0,5 %           |
| API     | CPU saturation       | < 80 %            | 30 j    | `container_cpu_usage_seconds_total`       | —               |

Adapter cette fiche selon les métriques réellement exposées et les dashboards Grafana créés.
