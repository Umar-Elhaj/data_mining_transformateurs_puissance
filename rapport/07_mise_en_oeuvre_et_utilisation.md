# Mise en œuvre et utilisation

## Méthodologie suivie

Nous avons organisé le projet en une chaîne d'étapes. Chacune part des résultats de la précédente :

| Étape | Ce que nous avons fait | Outils | Résultat |
|---|---|---|---|
| 1. Collecte | Nous nous sommes connectés à la base Access en lecture seule, nous avons inventorié les 34 tables et extrait les 12 tables utiles | pyodbc, pandas | 12 fichiers CSV dans `data/raw/` |
| 2. Prétraitement | Nous avons construit la table des gaz dissous des transformateurs, puis nous avons traité les faux zéros, les valeurs manquantes, les doublons et les valeurs aberrantes. Nous avons enfin appliqué un logarithme, une standardisation et une ACP | pandas, numpy, scikit-learn | `dga_transformateurs_pretraite.csv` (4 338 prélèvements) |
| 3. Fouille des données | Nous avons construit la variable cible « action recommandée » à partir de l'avis de l'expert, puis nous avons comparé KNN, arbre de décision et forêt aléatoire. Nous avons ensuite regroupé les prélèvements avec K-Means, CAH et DBSCAN | scikit-learn, scipy | Meilleur modèle : forêt aléatoire (AUC = 0,82) ; 2 groupes ; 6 % de prélèvements atypiques |
| 4. Règles d'association | Nous avons transformé les gaz en items (seuils IEEE), extrait les itemsets fréquents avec Apriori et généré les règles | mlxtend | Règles du type « CH4 + C2H4 élevés → action (62 %) » |
| 5. Visualisation et interprétation | Nous avons regroupé les graphiques clés et ajouté l'état du parc, l'évolution des gaz et la comparaison des sites | matplotlib, seaborn | 9 figures dans `rapport/figures/` |
| 6. Recommandations | Nous avons traduit chaque constat en action de maintenance | — | 8 recommandations classées par priorité |

## Architecture du projet

```
project/
├── HuileData2003(1).mdb              Base de données source (jamais modifiée)
├── requirements.txt                  Bibliothèques Python nécessaires
├── README.md                         Guide du projet
├── 01_collecte_preparation.ipynb     Étape 1
├── 02_pretraitement.ipynb            Étape 2
├── 03_fouille_des_donnees.ipynb      Étape 3
├── 04_regles_association.ipynb       Étape 4
├── 05_visualisation_interpretation.ipynb   Étape 5
├── data/
│   ├── raw/                          Données brutes (une copie CSV par table)
│   └── processed/                    Données prétraitées
└── rapport/                          Rapports de chaque étape et figures
```

## Technologies utilisées

| Outil | Rôle |
|---|---|
| Python 3 et Jupyter Notebook | Langage et environnement de travail |
| pyodbc + pilote Microsoft Access | Lecture de la base `.mdb` |
| pandas, numpy | Manipulation des tableaux de données et calculs |
| scikit-learn | Normalisation, standardisation, ACP, classification et clustering |
| scipy | Dendrogramme de la CAH |
| mlxtend | Algorithme Apriori et règles d'association |
| matplotlib, seaborn | Graphiques |

## Utilisation

**Installation :**

1. Installer Python (version 3.10 ou plus récente).
2. Installer les bibliothèques avec la commande `pip install -r requirements.txt`.
3. Pour le notebook 01 uniquement, installer le pilote **Microsoft Access Database Engine**, dans la même version 32 ou 64 bits que Python.

**Exécution :** ouvrir les notebooks depuis le dossier du projet et les exécuter **dans l'ordre, de 01 à 05**.

| Notebook | Lit | Produit |
|---|---|---|
| 01 | la base `.mdb` | les CSV de `data/raw/` |
| 02 | `data/raw/` | la table prétraitée de `data/processed/` |
| 03 et 04 | la table prétraitée et l'avis de l'expert | les modèles, les groupes et les règles (affichés dans le notebook) |
| 05 | les mêmes fichiers | les 9 figures de `rapport/figures/` |

Les notebooks 02 à 05 ne lisent que des fichiers CSV. Ils fonctionnent donc sans Access, sur n'importe quel système.
