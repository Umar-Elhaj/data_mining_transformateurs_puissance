# Data Mining — Transformateurs de puissance

**Thème :** collecte des données, data mining, visualisation et interprétation des données des transformateurs de puissance.

Le projet analyse l'historique des analyses d'huile isolante (1984 – 2009) stocké dans la base Access `HuileData2003.mdb`. Il se concentre sur les **gaz dissous** des **transformateurs** et aboutit à des recommandations de maintenance.

## Architecture du projet

```
project/
├── HuileData2003(1).mdb                  Base de données source (Microsoft Access), jamais modifiée
├── requirements.txt                      Bibliothèques Python nécessaires
├── README.md                             Ce fichier
│
├── 01_collecte_preparation.ipynb         Étape 1 : connexion à la base, choix et extraction des tables
├── 02_pretraitement.ipynb                Étape 2 : qualité des données, NA, doublons, aberrantes, log, standardisation, ACP
├── 03_fouille_des_donnees.ipynb          Étape 3 : classification (KNN, arbre, forêt) et clustering (K-Means, CAH, DBSCAN)
├── 04_regles_association.ipynb           Étape 4 : règles d'association (Apriori)
├── 05_visualisation_interpretation.ipynb Étape 5 : graphiques clés et interprétations finales
│
├── data/
│   ├── raw/                              Données brutes : une copie CSV de chaque table de la base (non traitées)
│   └── processed/
│       └── dga_transformateurs_pretraite.csv   Données traitées : table finale de l'étape 2, utilisée par les étapes 3 à 5
│
└── rapport/
    ├── 01_collecte_des_donnees.md        Rapport de chaque étape (base du Word et du PowerPoint)
    ├── 02_pretraitement_des_donnees.md
    ├── 03_fouille_des_donnees.md
    ├── 04_regles_association.md
    ├── 05_visualisation_interpretation.md
    ├── 06_recommandations.md             Recommandations finales
    └── figures/                          Les 9 graphiques clés en PNG (fig01 à fig09), créés par le notebook 05
```

## Installation

Il faut **Python 3.10 ou plus récent**. Pour installer les bibliothèques, on lance :

```bash
pip install -r requirements.txt
```

Seul le **notebook 01** lit la base `.mdb`. Il faut pour cela le pilote **Microsoft Access** (*Microsoft Access Database Engine*), dans la même version 32 ou 64 bits que Python. C'est le cas par défaut sur un Windows où Office est installé.

Les notebooks 02 à 05 lisent uniquement les fichiers CSV de `data/`. Ils fonctionnent donc sans Access, sur n'importe quel système.

## Exécution

Ouvrir les notebooks **dans l'ordre (01 → 05)** depuis le dossier du projet. Chaque notebook utilise les fichiers produits par le précédent :

| Notebook | Lit | Produit |
|---|---|---|
| 01 | `HuileData2003(1).mdb` | `data/raw/*.csv` |
| 02 | `data/raw/` (Equipement, Analyse, Gaz_Dissous) | `data/processed/dga_transformateurs_pretraite.csv` |
| 03 | table prétraitée + `data/raw/` (AnalyseDetail, Recommandation) | résultats dans le notebook |
| 04 | table prétraitée + `data/raw/` (AnalyseDetail, Recommandation, NormesC57104) | résultats dans le notebook |
| 05 | les mêmes fichiers | `rapport/figures/fig01…fig09.png` |

## Résultats en bref

- **4 338 prélèvements** de gaz dissous sur **292 transformateurs**, après prétraitement.
- **Classification :** la forêt aléatoire retrouve 51 % des actions recommandées par l'expert (AUC = 0,82).
- **Clustering :** 2 familles de prélèvements, les peu gazés et les plus gazés. DBSCAN isole 6 % de prélèvements atypiques.
- **Règles d'association :** quand CH4 et C2H4 sont élevés ensemble, une action est recommandée dans 62 % des cas, contre 7 % en moyenne.
- **Recommandations :** 8 actions, de l'intervention urgente sur T0218C à l'enregistrement des pannes réelles (voir `rapport/06_recommandations.md`).
