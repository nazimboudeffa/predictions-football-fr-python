# Cache Directory

Ce dossier contient les fichiers de cache pour les appels API-Football.

## Structure

- Chaque requête API est mise en cache dans un fichier JSON unique
- Les noms de fichiers sont des hash MD5 pour garantir l'unicité
- Les fichiers sont automatiquement créés lors des appels API
- Les données en cache sont valides pendant 1 heure par défaut

## Gestion

- **Lecture automatique** : Le système vérifie automatiquement si des données sont en cache
- **Nettoyage** : Utilisez `api.clear_cache()` dans le notebook pour vider le cache
- **Taille** : Vérifiez l'espace utilisé avec `api.get_cache_stats()`

## Avantages

- ✅ Économise vos appels API (quota de 100/jour)
- ✅ Persistant entre les sessions
- ✅ Réponses instantanées pour les données en cache
- ✅ Pas besoin de reconfigurer à chaque démarrage

## Note

Les fichiers `*.json` de ce dossier sont ignorés par Git pour éviter de stocker des données potentiellement volumineuses dans le dépôt.
