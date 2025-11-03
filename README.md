# M2 IR — Conteneurisation & Orchestration (S4 → S7)

Ce dépôt contient les livrables de TP pour le module **Conteneurisation & Orchestration**
(Ingress/TLS, persistance, scalabilité & résilience, observabilité) sur Kubernetes.

## 📁 Structure du dépôt

- `S4-ingress-tls-config/` : exposition L7, TLS, ConfigMap, Secret, rollback
- `S5-statefulset-persistence/` : PostgreSQL en StatefulSet avec persistance et runbook backup/restore
- `S6-scalabilite-resilience/` : HPA, PDB, SLO/SLI, scénario de charge k6, canary (Argo Rollouts)
- `S7-observabilite/` : Prometheus, Grafana, Loki, Jaeger, alerting

Namespace de travail recommandé : `workshop`.

## ⚙️ Prérequis

- Cluster Kubernetes (kind / minikube / k3d / autre)
- `kubectl`, `helm`, `k6`
- Ingress NGINX Controller
- cert-manager
- (S6 — bonus) Prometheus Adapter pour métriques custom
- (S7) kube-prometheus-stack, Loki, Jaeger Operator

## 🚀 Déploiement rapide (ordre conseillé)

```bash
# S4 : Ingress, TLS, Config, Secret
kubectl apply -f S4-ingress-tls-config/

# S5 : PostgreSQL en StatefulSet
kubectl apply -f S5-statefulset-persistence/

# S6 : Scalabilité & résilience
kubectl apply -f S6-scalabilite-resilience/

# S7 : Observabilité (après installation des charts Helm)
kubectl apply -f S7-observabilite/
```

⚠️ Tous les manifests sont des starters : adapte les images, probes, ressources et `storageClassName`
en fonction de ton cluster.
