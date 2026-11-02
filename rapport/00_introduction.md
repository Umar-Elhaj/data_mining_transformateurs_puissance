# Introduction

## Contexte

Le transformateur de puissance est un équipement clé des réseaux électriques. Son huile isolante joue deux rôles : elle **isole** électriquement les parties sous tension et elle **refroidit** l'appareil. Quand le transformateur vieillit ou subit un défaut (surchauffe, décharges partielles, arc électrique), l'huile et le papier isolant se dégradent et libèrent des **gaz dissous**. Analyser régulièrement ces gaz permet donc de surveiller l'état d'un transformateur **sans l'arrêter**.

Nous disposons de la base de données **HuileData2003.mdb** (Microsoft Access). Elle contient l'historique des analyses d'huile réalisées entre **1984 et 2009** sur **900 équipements**, dont **432 transformateurs**, répartis sur quatre sites (CCD, CCP, CCS et CIM).

## Problématique

Comment exploiter cet historique d'analyses pour **évaluer l'état des transformateurs**, **repérer les situations à risque** et **orienter les décisions de maintenance** ?

## Objectifs

Nous avons fixé cinq objectifs :

1. **collecter** les données utiles de la base Access ;
2. **préparer** des données fiables (qualité, valeurs manquantes, doublons, valeurs aberrantes) ;
3. **fouiller** les données avec des méthodes supervisées, non supervisées et des règles d'association ;
4. **visualiser et interpréter** les résultats ;
5. **formuler des recommandations** concrètes pour le service maintenance.

## Fonctionnalités du projet

Le projet que nous avons réalisé permet de :

| Fonctionnalité | Description |
|---|---|
| Extraction des données | Lire la base Access et exporter les tables utiles en fichiers CSV |
| Préparation des données | Produire une table propre de 4 338 prélèvements de gaz dissous sur 292 transformateurs |
| Prédiction de la décision de l'expert | Estimer, à partir des gaz, si un prélèvement nécessite une action |
| Segmentation et détection d'anomalies | Regrouper les prélèvements selon leur profil de gaz et isoler les profils atypiques |
| Règles d'association | Identifier les combinaisons de gaz qui apparaissent ensemble et celles qui mènent à une action |
| Diagnostic du parc | Classer chaque transformateur selon la norme IEEE C57.104 et repérer les priorités |
| Recommandations | Proposer des actions de maintenance classées par priorité |

## Démarche

Nous avons suivi six étapes, présentées dans les chapitres suivants : collecte des données, prétraitement, fouille des données, règles d'association, visualisation et interprétation, puis recommandations. La mise en œuvre technique et l'utilisation du projet sont décrites dans le chapitre « Mise en œuvre et utilisation ».
