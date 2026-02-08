# Prédictions Football Ligue 1

Projet Python pour prédire les résultats des matchs de Ligue 1 française en utilisant l'API-Football.

## Installation

1. Créer et activer un environnement virtuel :
```powershell
# Windows PowerShell
python -m venv .venv
.venv\Scripts\Activate.ps1

# Linux/Mac
python3 -m venv .venv
source .venv/bin/activate
```

2. Installer les dépendances :
```bash
pip install -r requirements.txt
```

3. Obtenir une clé API gratuite sur [API-Football](https://www.api-football.com/)

4. Configurer les variables d'environnement :
   - Copier le fichier `.env.example` vers `.env`
   - Remplacer `votre_clé_api_ici` par votre clé API dans le fichier `.env`

```bash
cp .env.example .env
# Ensuite éditez .env avec votre clé API
```

## Utilisation

Le projet est organisé en **notebooks séquentiels** :

### 📥 Étape 1 : Collecte des Données

**`0_collecte_donnees.ipynb`** - À exécuter en premier

Collecte et met en cache toutes les données nécessaires depuis l'API-Football.

```bash
jupyter notebook 0_collecte_donnees.ipynb
```

**Contenu :**
- Récupération de la liste des équipes
- Statistiques détaillées de chaque équipe
- Classement de la Ligue 1
- Head-to-head des principales confrontations
- **~30 appels API** (conservés en cache 24h)

⚠️ **À exécuter avant les notebooks de prédiction !**

---

### 🎯 Étape 2 : Prédictions (au choix)

Une fois les données en cache, choisissez votre approche de prédiction :

#### 1️⃣ Approche Simple (`1_predictions_simples.ipynb`)

Prédiction basée sur un score composite :

```bash
jupyter notebook 1_predictions_simples.ipynb
```

**Caractéristiques** :
- ✅ Formule transparente : `Score = Forme + (Buts - Encaissés) × 0.5`
- ✅ Utilise uniquement le cache (0 appels API)
- ✅ Rapide et simple à comprendre
- ❌ Ne prend pas en compte domicile/extérieur
- 📖 [Documentation de la méthode](docs/prediction-simple.md)
- 🎯 Précision : ~55-60%

#### 2️⃣ Approche Statistiques Avancées (`2_predictions_stats_avancees.ipynb`)

Scoring multi-facteurs sans ML :

```bash
jupyter notebook 2_predictions_stats_avancees.ipynb
```

**Caractéristiques** :
- ✅ 6 facteurs (forme, attaque, défense, position, domicile, H2H)
- ✅ Transparent et explicable
- ✅ Utilise uniquement le cache (0 appels API)
- ✅ Probabilités estimées
- ✅ Distinction domicile/extérieur
- 🎯 Précision : ~60-65%

#### 3️⃣ Approche Machine Learning (`3_predictions_ml.ipynb`)

Modèles ML avec entraînement sur historique :

```bash
jupyter notebook 3_predictions_ml.ipynb
```

**Caractéristiques** :
- 🤖 Random Forest, Régression Logistique
- 📈 Entraînement sur 3 saisons (requiert collecte supplémentaire)
- 🎲 Probabilités et niveaux de confiance
- 📊 Validation avec métriques
- ⏱️ Temps d'entraînement : ~10-15min
- 🎯 Précision : ~65-70%

#### 4️⃣ Approche Classement (`4_predictions_classement.ipynb`)

Prédiction probabiliste basée sur le rang au classement :

```bash
jupyter notebook 4_predictions_classement.ipynb
```

**Caractéristiques** :
- 🏆 Formule mathématique : `P(victoire) = (1/rang) normalisé`
- ✅ Probabilités cohérentes (somme = 100%)
- ✅ Utilise uniquement le cache (0 appels API)
- ✅ Extrêmement simple et transparent
- 📊 Visualisations (graphiques, heatmap)
- ❌ Ignore la forme récente et le contexte
- 📖 [Documentation de la méthode](docs/prediction_classement.md)
- 🎯 Précision : ~50-55% (baseline)

---

### 💻 Utilisation dans VS Code

Ouvrez simplement les fichiers `.ipynb` dans VS Code avec l'extension Jupyter installée.

**Ordre recommandé :**
1. `0_collecte_donnees.ipynb` (une fois par jour)
2. `1_predictions_simples.ipynb` OU `2_predictions_stats_avancees.ipynb` OU `3_predictions_ml.ipynb`

## Fonctionnalités

- Récupération des matchs de Ligue 1
- Statistiques des équipes (globales et domicile/extérieur)
- Classement en temps réel
- Historique des confrontations
- **Deux systèmes de prédiction** : simple et ML
- **Système de cache intelligent** : économise les appels API en sauvegardant les données sur disque

## Système de cache

Pour optimiser l'utilisation de votre quota API (100 requêtes/jour avec le plan gratuit), le projet intègre un système de cache sur disque :

- **Cache persistant** : Les données sont sauvegardées dans le dossier `cache/` sous forme de fichiers JSON
- **Cache automatique** : Les données sont automatiquement mises en cache pendant 1 heure
- **Persistance** : Le cache reste disponible même après fermeture et réouverture du notebook
- **Indicateurs visuels** : 
  - 📦 = Données récupérées du cache fichier
  - 🌐 = Appel API réel effectué
  - 💾 = Données sauvegardées dans le cache
- **Compteur d'appels** : Suivi du nombre d'appels API réels effectués
- **Gestion du cache** :
  ```python
  api.get_cache_stats()  # Afficher les statistiques (nombre de fichiers, taille, etc.)
  api.clear_cache()      # Vider le cache si besoin
  ```

## API Utilisée

**API-Football** (100 requêtes/jour gratuites)
- Documentation: https://www.api-football.com/documentation-v3
- Ligue 1 ID: 61

## Comparaison des Approches

| Critère | Approche Classement | Approche Simple | Approche Stats Avancées | Approche ML |
|---------|---------------------|----------------|------------------------|-------------|
| **Complexité** | Très faible | Faible | Moyenne | Élevée |
| **Temps de calcul** | Instantané | Instantané | Instantané | ~10-15 min (entraînement) |
| **Données historiques** | Non requises | Non requises | Non requises | Oui (3 saisons) |
| **Features** | 1 (rang) | 3 (forme, buts) | 6 (forme, H2H, domicile) | 20+ (stats détaillées) |
| **Sortie** | Probabilités normalisées | Score + seuil | Probabilités estimées | Probabilités + classe |
| **Validation** | Aucune | Aucune | Aucune | Train/test split |
| **Domicile/Extérieur** | Non | Non | Oui | Oui |
| **Appels API** | Minimal (cache) | Minimal (cache) | Minimal (cache) | Important (entraînement) |
| **Précision estimée** | ~50-55% | ~55-60% | ~60-65% | ~65-70% |
| **Recommandé pour** | Baseline/Référence | Découverte | Analyse équilibrée | Production |

## Structure du Projet

```
predictions-football-fr-python/
├── 0_collecte_donnees.ipynb           # 📥 ÉTAPE 1: Collecte et cache des données
├── 1_predictions_simples.ipynb        # 🎯 Prédictions simples (lecture cache)
├── 2_predictions_stats_avancees.ipynb # 📊 Prédictions stats avancées (lecture cache)
├── 3_predictions_ml.ipynb             # 🤖 Prédictions ML (avec entraînement)
├── 4_predictions_classement.ipynb     # 🏆 Prédictions par classement (lecture cache)
├── predictions_ligue1.ipynb           # 📜 Archive: ancien notebook complet
├── .env                               # Configuration (non versionné)
├── .env.example                       # Template de configuration
├── requirements.txt                   # Dépendances Python
├── cache/                             # Cache des requêtes API
│   ├── README.md                      # Documentation du cache
│   └── *.json                         # Fichiers cache (non versionnés)
├── models/                            # Modèles ML sauvegardés (non versionnés)
│   ├── random_forest_model.pkl
│   ├── scaler.pkl
│   └── feature_columns.pkl
└── docs/                              # Documentation
    ├── README.md                      # Index de la documentation
    ├── prediction_simple_1.md         # Méthodologie approche simple
    ├── predictions_simple_2.md        # Méthodologie approche simple (v2)
    └── prediction_classement.md       # Méthodologie approche classement
```

### 🔄 Workflow Recommandé

1. **Première utilisation** :
   ```bash
   # Créer l'environnement
   python -m venv .venv
   .venv\Scripts\Activate.ps1  # Windows
   pip install -r requirements.txt
   
   # Configurer .env avec votre clé API
   cp .env.example .env
   # Éditer .env
   
   # Collecter les données
   jupyter notebook 0_collecte_donnees.ipynb
   ```

2. **Utilisation quotidienne** :
   ```bash
   # Rafraîchir les données (1 fois par jour max)
   jupyter notebook 0_collecte_donnees.ipynb
   
   # Faire des prédictions (choix de l'approche)
   jupyter notebook 1_predictions_simples.ipynb
   # OU
   jupyter notebook 2_predictions_stats_avancees.ipynb
   # OU
   jupyter notebook 3_predictions_ml.ipynb
   # OU
   jupyter notebook 4_predictions_classement.ipynb
   ```

## Sécurité

⚠️ **Important** : Ne committez jamais le fichier `.env` contenant votre clé API. Il est déjà inclus dans `.gitignore`.
