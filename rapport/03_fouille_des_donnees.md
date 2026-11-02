# Étape 3 — Fouille des données

## 3.1 Objectif

Cette étape extrait de l'information des analyses de gaz dissous des transformateurs avec deux approches :

- la **classification supervisée** : prédire, à partir des gaz, si l'expert a recommandé une action ;
- le **clustering** (non supervisé) : regrouper les prélèvements dont les profils de gaz se ressemblent, sans utiliser l'avis de l'expert.

**Données :** la table prétraitée à l'étape 2 (4 338 prélèvements, 292 transformateurs). Nous y avons ajouté l'avis de l'expert, tiré des tables `AnalyseDétail` et `Recommandation`.

## 3.2 Construction de la variable cible

La base ne contient **aucune colonne « panne »**. En revanche, pour chaque prélèvement, l'expert a choisi une recommandation :

| Table | Colonne | Contenu |
|---|---|---|
| `AnalyseDétail` | `CodeRecommandation` | Code choisi par l'expert pour le prélèvement |
| `Recommandation` | `Recommandation` | Texte du code, pour l'essai GD (un même code a un sens différent selon l'essai) |

**Règle :** `action` = 0 si le texte est « Aucune action », 1 pour toute autre recommandation, et inconnu s'il n'y a pas de texte.

| Valeur | Prélèvements |
|---|---:|
| 0 — Aucune action | 3 610 |
| 1 — Action recommandée | 260 (6,7 %) |
| Inconnue (exclue) | 468 |

Les trois quarts des actions (195 sur 260) sont une demande de **nouveau prélèvement** (« Reprise d'une seringue pour l'analyse GD »). Les interventions lourdes sont rares : nous comptons 8 mises hors service immédiates. La cible signifie donc « **l'expert a jugé que ce prélèvement demandait une suite** », et non « le transformateur est en panne ».

## 3.3 Classification supervisée

**Variables d'entrée :** les 7 gaz de défaut après logarithme (H2, CH4, C2H2, C2H4, C2H6, CO, CO2).

**Validation chronologique :** les modèles apprennent sur les 80 % de prélèvements les plus anciens et sont testés sur les 20 % les plus récents.

| Ensemble | Période | Prélèvements | Part d'actions |
|---|---|---:|---:|
| Entraînement | 1984 – janvier 2005 | 3 095 | 7,3 % |
| Test | janvier 2005 – 2009 | 775 | 4,5 % (35 actions) |

La standardisation est calculée sur l'entraînement seulement, pour éviter toute fuite d'information. Comme les actions sont rares, l'arbre et la forêt donnent plus de poids à cette classe (`class_weight="balanced"`).

| Modèle | Actions retrouvées (sur 35) | Fausses alertes | Rappel | Précision | F1 | AUC |
|---|---:|---:|---:|---:|---:|---:|
| KNN (k = 15) | 4 | 7 | 0,11 | 0,36 | 0,17 | 0,80 |
| Arbre de décision (profondeur 5) | 13 | 103 | 0,37 | 0,11 | 0,17 | 0,54 |
| **Forêt aléatoire (300 arbres)** | **18** | 64 | **0,51** | 0,22 | **0,31** | **0,82** |

- Le **KNN** répond presque toujours « aucune action » : il ne peut pas donner plus de poids à la classe rare.
- L'**arbre de décision** se généralise mal à la période récente : son AUC (0,54) est proche du hasard.
- La **forêt aléatoire** est le meilleur modèle. Elle retrouve la moitié des actions et classe bien les prélèvements à risque (AUC = 0,82).

Les gaz expliquent une bonne partie de la décision de l'expert, mais pas tout : celui-ci tient aussi compte de l'historique du transformateur et des autres essais. La baisse de la part d'actions entre l'entraînement et le test (7,3 % → 4,5 %) montre aussi que les pratiques ont évolué.

**Importance des gaz (forêt aléatoire) :** H2 20 %, CH4 19 %, C2H6 16 %, C2H2 13 %, C2H4 12 %, CO 11 %, CO2 9 %. Les décisions réagissent surtout aux gaz de **défaut actif** (décharges, surchauffe, arc), beaucoup moins au vieillissement du papier (CO, CO2).

## 3.4 Clustering

Le clustering porte sur les 7 gaz standardisés de l'étape 2 et sur les 4 338 prélèvements.

### K-Means

| k | 2 | 3 | 4 | 5 | 6 | 7 |
|---|---|---|---|---|---|---|
| Silhouette | **0,33** | 0,32 | 0,29 | 0,30 | 0,30 | 0,23 |

Nous avons retenu **2 groupes**. Une silhouette de 0,33 indique une séparation faible : les prélèvements forment plutôt un continuum que des familles bien distinctes.

| | Groupe 0 | Groupe 1 |
|---|---|---|
| Prélèvements | 2 112 | 2 226 |
| Médianes | H2 = 7 ppm, CO2 = 1 596 ppm | 2 à 9 fois plus élevées : H2 = 31 ppm, CO2 = 4 305 ppm |
| Action recommandée par l'expert | 2 % | 11,5 % |

K-Means sépare les prélèvements **peu gazés** des prélèvements **plus gazés**. Sans avoir vu la décision de l'expert, il retrouve une partie de sa logique : le groupe 1 reçoit près de 6 fois plus souvent une recommandation d'action.

### CAH (méthode de Ward, sur un échantillon de 1 000 prélèvements)

La plus grande marche du dendrogramme est la dernière fusion (distance ≈ 70, contre ≈ 35 pour la précédente). La silhouette est aussi maximale pour 2 groupes (0,33). La CAH **confirme les 2 familles** trouvées par K-Means.

### DBSCAN (min_samples = 10)

| eps | Groupes | Points isolés (bruit) |
|---:|---:|---:|
| 0,8 | 11 | 519 (12,0 %) |
| **1,0** | 7 | **273 (6,3 %)** |
| 1,4 | 2 | 116 (2,7 %) |

Avec eps = 1,0, les **points isolés** ont un profil inhabituel. Leurs médianes contiennent plus d'acétylène (0,8 ppm contre 0), d'hydrogène (29 contre 16 ppm) et d'éthane (7,1 contre 3,0 ppm). L'expert recommande une action pour **21 %** d'entre eux, contre **6 %** pour les autres. DBSCAN joue ici le rôle de **détecteur d'anomalies**.

## 3.5 Bilan

| Méthode | Résultat principal |
|---|---|
| Classification | Forêt aléatoire = meilleur modèle : 51 % des actions retrouvées, AUC = 0,82 |
| Importance des gaz | H2, CH4 et C2H6 pèsent le plus ; CO et CO2 le moins |
| K-Means et CAH | 2 familles : peu gazés (2 % d'actions) et plus gazés (11,5 % d'actions) |
| DBSCAN | 6,3 % de prélèvements atypiques, 3,5 fois plus souvent jugés à surveiller |

**Limites :**
- La cible est une décision d'expert, pas une panne constatée.
- Les modèles ne regardent qu'un prélèvement à la fois, sans tenir compte de l'évolution des gaz dans le temps.
- Les groupes se chevauchent : ils décrivent des tendances, pas des catégories tranchées.
