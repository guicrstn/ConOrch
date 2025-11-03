# Rollback express — Déploiement `front`

Ce fichier démontre un scénario de rollback rapide sur le déploiement `front`.

## 1. Casser volontairement le déploiement

On force une mauvaise image afin de provoquer un échec de déploiement :

```bash
kubectl -n workshop set image deployment/front front=nginx:broken
kubectl -n workshop rollout status deploy/front
```

La commande `rollout status` doit montrer que le déploiement ne progresse pas correctement.

## 2. Revenir à la version précédente

```bash
kubectl -n workshop rollout undo deploy/front
kubectl -n workshop rollout status deploy/front
```

Le déploiement revient à la révision précédente, avec les pods fonctionnels.

## 3. Astuces

- `kubectl -n workshop rollout history deploy/front` : voir l'historique des révisions
- Utiliser des labels/annotations de version (ex: `app.kubernetes.io/version`) pour tracer les releases
