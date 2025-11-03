# Installation rapide de la stack d’observabilité

Namespace recommandé : `observability`

```bash
# kube-prometheus-stack (Prometheus, Alertmanager, Grafana)
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install monitor prometheus-community/kube-prometheus-stack -n observability --create-namespace

# Loki + Promtail
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install loki grafana/loki-stack -n observability --set promtail.enabled=true

# Jaeger Operator
kubectl create ns observability || true
kubectl apply -n observability -f https://github.com/jaegertracing/jaeger-operator/releases/latest/download/jaeger-operator.yaml

# Instance Jaeger minimaliste
cat <<'YAML' | kubectl apply -n observability -f -
apiVersion: jaegertracing.io/v1
kind: Jaeger
metadata:
  name: simplest
spec:
  strategy: allinone
YAML
```

Une fois déployé :
- Accéder à Grafana via le Service exposé par le chart
- Configurer une datasource Prometheus + une datasource Loki
- Vérifier l’ingestion des métriques et des logs
- Vérifier que Jaeger reçoit bien des traces (via auto‑instrumentation OpenTelemetry si dispo)
