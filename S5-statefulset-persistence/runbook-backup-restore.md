# Runbook backup / restore PostgreSQL (StatefulSet)

Namespace : `workshop` — StatefulSet : `postgres`

## 1. Identifier le pod PostgreSQL

```bash
POD=$(kubectl -n workshop get pod -l app=postgres -o jsonpath='{.items[0].metadata.name}')
echo "$POD"
```

## 2. Backup logique (dump complet)

```bash
kubectl -n workshop exec -it "$POD" -- bash -lc 'pg_dumpall -U postgres' > backup-$(date +%F).sql
```

Un fichier `backup-YYYY-MM-DD.sql` est généré localement.

## 3. Restore à partir d’un dump

```bash
kubectl -n workshop exec -i "$POD" -- bash -lc 'psql -U postgres' < backup-YYYY-MM-DD.sql
```

⚠️ Le restore peut écraser des données existantes : à utiliser avec précaution.

## 4. Option (TP avancé) — Velero

Si Velero est installé et configuré :

```bash
velero backup create workshop --include-namespaces workshop --wait --default-volumes-to-restic
velero restore create --from-backup workshop
```

Documenter dans le rapport :
- Les commandes exécutées
- Où sont stockés les backups
- Comment tester la restauration (ex: création d’une table puis vérification post-restore)
