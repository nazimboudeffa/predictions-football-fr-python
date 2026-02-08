# 🎯 Prédiction Simple basée sur les Statistiques

## Vue d'ensemble

Cette méthode de prédiction utilise une approche statistique simple pour estimer le résultat probable d'un match entre deux équipes de Ligue 1. Elle combine la **forme récente** des équipes et leur **différence de buts** pour calculer un score composite.

## Méthodologie

### 1. Collecte des Données

Pour chaque équipe, nous récupérons via l'API-Football :
- 🎯 **Buts marqués** (`goals.for.total.total`)
- 🥅 **Buts encaissés** (`goals.against.total.total`)
- 📍 **Forme récente** (`form`) - chaîne de caractères avec les derniers résultats (ex: "WWDLW")

### 2. Calcul du Score de Forme

La fonction `calculate_form_score()` convertit la forme récente en score numérique :

```python
def calculate_form_score(form_string):
    """Calcule un score basé sur la forme récente"""
    score = 0
    for result in form_string:
        if result == 'W':    # Victoire (Win)
            score += 3
        elif result == 'D':  # Match nul (Draw)
            score += 1
        # L (Loss) = 0 point
    return score
```

**Système de points :**
- ✅ **W (Win)** = 3 points
- 🟡 **D (Draw)** = 1 point
- ❌ **L (Loss)** = 0 point

**Exemple :** 
- Forme "WWDLW" → 3 + 3 + 1 + 0 + 3 = **10 points**
- Forme "WDLDL" → 3 + 1 + 0 + 1 + 0 = **5 points**

### 3. Calcul du Score Composite

Pour chaque équipe, nous calculons un **score composite** qui combine :

```python
score = forme + (buts_marqués - buts_encaissés) × 0.5
```

**Composantes :**
1. **Score de forme** : Reflète les performances récentes
2. **Différence de buts pondérée** : 
   - Multipliée par **0.5** pour équilibrer son poids par rapport à la forme
   - Une équipe qui marque beaucoup mais encaisse peu aura un bonus
   - Une équipe en difficulté défensive sera pénalisée

**Exemple de calcul :**

| Équipe | Forme | Score Forme | Buts Marqués | Buts Encaissés | Différence | Score Composite |
|--------|-------|-------------|--------------|----------------|------------|-----------------|
| PSG    | WWWWW | 15          | 45           | 12             | +33        | 15 + (33 × 0.5) = **31.5** |
| Monaco | WWDLW | 10          | 38           | 20             | +18        | 10 + (18 × 0.5) = **19.0** |

### 4. Établissement de la Prédiction

La prédiction finale compare les scores composites des deux équipes :

```python
if score_equipe1 > score_equipe2 + 2:
    → "🏆 Victoire probable de l'équipe 1"
elif score_equipe2 > score_equipe1 + 2:
    → "🏆 Victoire probable de l'équipe 2"
else:
    → "🤝 Match serré, résultat incertain"
```

**Seuil de décision :**
- Une différence de **+2 points** minimum est requise pour prédire une victoire
- Sinon, le match est considéré comme **incertain**

## Exemple Complet : PSG vs Marseille

### Données d'entrée
```
PSG:
- Forme: WWWDW (3+3+3+1+3 = 13)
- Buts marqués: 50
- Buts encaissés: 15
- Différence: +35

Marseille:
- Forme: WDLWD (3+1+0+3+1 = 8)
- Buts marqués: 42
- Buts encaissés: 28
- Différence: +14
```

### Calculs
```
Score PSG = 13 + (50 - 15) × 0.5 = 13 + 17.5 = 30.5
Score OM = 8 + (42 - 28) × 0.5 = 8 + 7.0 = 15.0

Différence = 30.5 - 15.0 = 15.5 > 2
```

### Prédiction
```
🏆 Victoire probable de Paris Saint Germain
```

## Avantages de cette Méthode

✅ **Simplicité** : Facile à comprendre et à interpréter  
✅ **Rapidité** : Calcul instantané sans modèle complexe  
✅ **Données objectives** : Basée sur des statistiques réelles  
✅ **Forme récente** : Prend en compte la dynamique actuelle des équipes  
✅ **Balance offensive/défensive** : Intègre les deux aspects du jeu  

## Limites et Améliorations Possibles

### ⚠️ Limites Actuelles

1. **Contexte ignoré** :
   - Pas de distinction domicile/extérieur
   - Blessures et suspensions non prises en compte
   - Motivation (derby, match de coupe) ignorée

2. **Pondération arbitraire** :
   - Le coefficient 0.5 pour la différence de buts est empirique
   - Le seuil de 2 points pour la prédiction est subjectif

3. **Historique limité** :
   - Seulement la forme récente (5-10 derniers matchs généralement)
   - Pas d'historique des confrontations directes

4. **Résultat binaire** :
   - Pas de probabilités ou de niveaux de confiance
   - Pas de prédiction de score exact

### 🚀 Améliorations Envisageables

1. **Facteur domicile/extérieur** :
   ```python
   if match_at_home:
       score += 1.5  # Bonus domicile
   ```

2. **Historique head-to-head** :
   - Intégrer le bilan des confrontations directes
   - Pondérer selon les résultats récents entre les deux équipes

3. **Statistiques avancées** :
   - xG (expected goals) : Qualité des occasions
   - Possession moyenne
   - Tirs cadrés
   - Passes réussies

4. **Machine Learning** :
   - Utiliser scikit-learn pour optimiser les pondérations
   - Modèle de régression logistique ou forêt aléatoire
   - Validation croisée sur données historiques

5. **Probabilités** :
   ```python
   proba_victoire_1 = score1 / (score1 + score2)
   proba_victoire_2 = score2 / (score1 + score2)
   ```

6. **Prédiction de score** :
   - Estimer le nombre de buts probables pour chaque équipe
   - Utiliser les moyennes de buts ajustées par la défense adverse

## Utilisation du Code

```python
# Importer les fonctions
from predictions_ligue1 import calculate_form_score, simple_prediction

# Faire une prédiction
simple_prediction(team1_id=85, team2_id=81)  # PSG vs Marseille
```

### Sortie
```
⚔️  Paris Saint Germain vs Olympique de Marseille

📊 Analyse:
   Paris Saint Germain: Forme=13, Buts=50, Encaissés=15
   Olympique de Marseille: Forme=8, Buts=42, Encaissés=28

💯 Scores de prédiction:
   Paris Saint Germain: 30.5
   Olympique de Marseille: 15.0

🏆 Victoire probable de Paris Saint Germain
```

## Conclusion

Cette méthode de prédiction simple constitue un **excellent point de départ** pour l'analyse de matchs de football. Bien qu'elle présente des limitations, elle offre une approche transparente et basée sur des données objectives.

Pour des prédictions plus précises, il est recommandé de :
1. Collecter davantage de données (xG, possession, etc.)
2. Intégrer des facteurs contextuels (domicile, blessures)
3. Utiliser des techniques de machine learning
4. Valider les prédictions sur un historique de matchs

Cette base méthodologique permet néanmoins de comprendre les principes fondamentaux de la prédiction sportive et peut servir de **baseline** pour évaluer des modèles plus complexes.

---

**Auteur** : Système de prédiction Ligue 1  
**Version** : 1.0  
**Date** : Février 2026  
**Source** : [predictions_ligue1.ipynb](../predictions_ligue1.ipynb)
