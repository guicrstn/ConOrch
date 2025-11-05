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
```
```bash
# S5 : PostgreSQL en StatefulSet
kubectl apply -f S5-statefulset-persistence/
```
```bash
# S6 : Scalabilité & résilience
kubectl apply -f S6-scalabilite-resilience/
```
```bash
# S7 : Observabilité (après installation des charts Helm)
kubectl apply -f S7-observabilite/
```

⚠️ Tous les manifests sont des starters : adapte les images, probes, ressources et `storageClassName`
en fonction de ton cluster.

## Retour d’expérience — TP S4 : Ingress, TLS, ConfigMap & Secrets
## 🎯 Objectif du TP

L’objectif du S4 était de mettre en place une architecture multi-services sur Kubernetes, intégrant :

Un Ingress NGINX avec règles de routage et TLS (certificat auto-signé)

Un frontend (Nginx Demo) et un backend (Httpbin) déployés dans un namespace workshop

Une ConfigMap et un Secret injectés dans les conteneurs

Une stratégie de rollback sur les déploiements

## ⚙️ Environnement technique

Système : macOS (Docker Desktop)

Cluster : Kubernetes intégré à Docker Desktop
(Remplacement de Minikube pour plus de stabilité et compatibilité ARM)

Outils : kubectl, Helm, cert-manager, ingress-nginx

## 🏗️ Déroulement du déploiement

Préparation du cluster :

Activation du cluster Kubernetes dans Docker Desktop

Installation d’ingress-nginx et de cert-manager via Helm

Ajout du domaine workshop.local dans /etc/hosts → 127.0.0.1

Déploiement du namespace et des ressources :

kubectl apply -f S4-ingress-tls-config/


Création des ressources suivantes :

Namespace : workshop

Secret : app-secrets

ConfigMap : app-config

Deployments : front, api

Services : front, api

Ingress : web

ClusterIssuer : selfsigned

Mise en place du TLS :

Le ClusterIssuer a permis la génération automatique du certificat web-tls via cert-manager.

Le site est ensuite accessible via HTTPS :

https://workshop.local/front

https://workshop.local/api/status/200

## Résolution des problèmes rencontrés :

❌ Erreur initiale “namespace not found” → Solution : réappliquer après création du namespace.

❌ 503 Service Temporarily Unavailable → Cause : ingress actif mais pods front non prêts.

❌ Page “Welcome to nginx” au lieu du front attendu → Cause : mauvaise image (nginx au lieu de ghcr.io/nginxdemos/hello:plain-text).
✅ Correction :

kubectl -n workshop set image deployment/front front=ghcr.io/nginxdemos/hello:plain-text


✅ Certificat TLS fonctionnel via cert-manager.

✅ Ingress réécrit les chemins correctement avec annotations :

nginx.ingress.kubernetes.io/rewrite-target: /$2
nginx.ingress.kubernetes.io/use-regex: "true"


Test du rollback :

kubectl -n workshop set image deployment/front front=nginx:broken
kubectl -n workshop rollout undo deployment/front


✅ Le rollback permet de revenir à la dernière version fonctionnelle sans interruption durable du service.

✅ Résultats finaux

https://workshop.local/front → Affiche le texte de l’image demo (Nginx Hello).

https://workshop.local/api/status/200 → Répond avec un code HTTP 200 via Httpbin.

Certificat auto-signé valide et routage HTTPS fonctionnel.

ConfigMap et Secret bien montés dans les pods.

Rollback validé avec succès.

## 💬 Analyse personnelle

Ce TP m’a permis de :

Mieux comprendre la gestion du trafic via Ingress et la logique de TLS sur Kubernetes.

Découvrir la séparation des environnements via namespaces et la configuration dynamique via ConfigMap/Secrets.

Approfondir les mécanismes de déploiement continu et de rollback.

Identifier les différences entre un cluster Minikube et Docker Desktop (drivers, Ingress par défaut, IPs, etc.).

💡 Ce TP m’a réellement aidé à consolider ma compréhension de l’orchestration Kubernetes et du déploiement de services web sécurisés.
