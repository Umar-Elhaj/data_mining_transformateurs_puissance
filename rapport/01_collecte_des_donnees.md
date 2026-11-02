# Étape 1 — Collecte des données

## 1.1 Source des données

Les données proviennent de la base **Microsoft Access `HuileData2003.mdb`**. Elle contient l'historique des **analyses d'huile isolante** effectuées entre 1984 et 2009 sur le parc d'équipements électriques de plusieurs centrales et postes.

L'huile d'un transformateur sert à la fois d'**isolant électrique** et de **fluide de refroidissement**. Quand l'équipement vieillit ou subit un défaut (surchauffe, arc électrique, décharges), l'huile et le papier isolant se dégradent et libèrent des gaz et des sous-produits chimiques. Des prélèvements d'huile réguliers, analysés en laboratoire, permettent donc de **surveiller l'état de santé** d'un transformateur sans l'arrêter.

## 1.2 Contenu de la base

La base contient **34 tables**, réparties en sept groupes :

| Groupe | Tables | Rôle |
|---|---|---|
| Équipements | `Équipement` | Le parc : 900 équipements |
| Prélèvements | `Analyse` | Un enregistrement par échantillon d'huile (11 703) |
| Résultats de laboratoire | `Gaz Dissous`, `EPL`, `Furane`, `DBPC`, `BPC`, `Métaux Dans Huiles` | Une table par type d'essai |
| Avis de l'expert | `AnalyseDétail`, `RésultatsAnalysesCritères`, `AnalysesRejetees` | Recommandations et diagnostics |
| Tables de codes | `TypeÉquipement`, `TypeAnalyse`, `Localisation`, `Recommandation`, `Motif`, `Lieu`, `Laboratoire`… | Traduction des codes en libellés |
| Normes | `NormesC57104`, `NormesAlcan`, `NormesPremiersCritères`… | Seuils d'interprétation |
| Application | `Sécurité`, `AccèsLocalisation`… | Utilisateurs et droits d'accès du logiciel |

## 1.3 Tables retenues pour l'étude

Nous avons retenu **12 tables** :

| Table | Lignes | Contenu | Utilité dans l'étude |
|---|---|---|---|
| `Équipement` | 900 | Type, site, tension, puissance, fabricant, année de chaque équipement | Isoler et décrire les transformateurs |
| `TypeÉquipement` | 5 | Traduction du code type (`T` = Transformateur…) | Lecture de `Équipement` |
| `Localisation` | 23 | Nom complet des sites et postes | Lecture de `Équipement` |
| `Analyse` | 11 703 | Prélèvement : équipement, date, type d'essai | Lien entre un équipement et ses résultats |
| `TypeAnalyse` | 10 | Traduction du code d'essai (`GD` = Gaz dissous…) | Lecture de `Analyse` |
| `Gaz Dissous` | 5 090 | Concentrations des gaz dans l'huile (ppm) | Mesure principale pour détecter les défauts |
| `EPL` | 3 619 | Rigidité diélectrique, eau, acidité, tension interfaciale, couleur | Vieillissement de l'huile |
| `Furane` | 341 | Produits de dégradation du papier isolant | Vieillissement du papier |
| `DBPC` | 1 346 | Antioxydant restant dans l'huile | Oxydation de l'huile |
| `AnalyseDétail` | 11 583 | Code de la recommandation de l'expert pour chaque prélèvement | Partie « recommandation » |
| `Recommandation` | 59 | Texte des recommandations | Lecture de `AnalyseDétail` |
| `NormesC57104` | 3 | Seuils de la norme IEEE C57.104 pour les gaz dissous | Partie « interprétation » |

**Relations entre les tables :** un équipement (`NoÉquipement`) a plusieurs prélèvements (`Analyse`). Chaque prélèvement est identifié par une clé (`CléAnalyse`) qui le relie à ses résultats dans la table de l'essai correspondant (`Gaz Dissous`, `EPL`, `Furane` ou `DBPC`) et à l'avis de l'expert (`AnalyseDétail`).

## 1.4 Tables écartées

| Tables | Raison |
|---|---|
| `ECL`, `EP`, `ESL`, `Hydran` | Vides (0 ligne) |
| `Sécurité`, `Securité`, `AccèsLocalisation` | Utilisateurs, mots de passe et droits du logiciel : sans intérêt pour l'analyse, et données sensibles |
| `BPC`, `Métaux Dans Huiles` | Hors sujet (pollution réglementaire) ou trop peu de données (116 lignes) |
| `NormesAlcan`, `NormesAlcanBKP`, `NormesPremiersCritères`, `Normes` | Normes internes ou redondantes ; nous retenons la norme internationale IEEE C57.104 |
| `RésultatsAnalysesCritères`, `AnalysesRejetees`, `CritèreÉvaluation`, `ContenuRéférenceNorme`, `NomÉquipement`, `Motif`, `Lieu`, `Laboratoire`, `ÉtatAnalyse` | Textes libres, informations administratives ou codes non nécessaires à l'étude |

## 1.5 Méthode d'extraction

- **Outil :** Python, avec la bibliothèque `pyodbc` (connexion au pilote ODBC Microsoft Access) et `pandas` (mise en tableaux).
- **Pourquoi ce choix :** c'est la méthode la plus directe sous Windows. Elle lit la base sans conversion manuelle, elle est reproductible (un simple script) et elle n'altère pas le fichier source, ouvert en **lecture seule**.
- **Étapes :**
  1. connexion à la base en lecture seule ;
  2. inventaire des 34 tables et de leur nombre de lignes ;
  3. sélection des 12 tables utiles ;
  4. chargement de chaque table par une requête SQL `SELECT * FROM [table]` ;
  5. sauvegarde d'une copie **CSV** de chaque table dans `data/raw/`, pour travailler ensuite sans Access.

Aucune donnée n'est modifiée à cette étape : les tables sont chargées telles quelles.

## 1.6 Périmètre de l'étude : les transformateurs

| Type d'équipement | Nombre |
|---|---|
| Traversée isolée | 439 |
| **Transformateur** | **432** |
| Disjoncteur | 24 |
| Réservoir | 5 |
| **Total** | **900** |

La base ne contient pas que des transformateurs. Conformément au thème, **l'étude portera sur les 432 transformateurs**. Le filtre sera appliqué à l'étape de préparation des données.
