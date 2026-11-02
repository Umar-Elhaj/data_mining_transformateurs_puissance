# Étape 6 — Recommandations

## 6.1 Destinataire et précaution

Ces recommandations s'adressent au **service maintenance et ingénierie** qui exploite le parc de transformateurs étudié. Chacune découle d'un constat chiffré des étapes précédentes.

**Précaution :** les données s'arrêtent en **2009**. Les recommandations décrivent ce que les données justifiaient à cette date. Avant toute action, l'état actuel de chaque transformateur doit être vérifié par un **nouveau prélèvement d'huile**.

## 6.2 Actions immédiates

### R1 — Traiter en urgence le transformateur T0218C

- **Constat :** T0218C (site CCP) est le seul transformateur en **condition 4** (critique) à son dernier prélèvement. Son TDCG atteint 6 768 ppm, avec 5 269 ppm d'éthylène, 1 135 ppm d'éthane et 124 ppm d'acétylène : c'est la signature d'une surchauffe sévère de l'huile, accompagnée d'un arc électrique (figure 7).
- **Recommandation :** refaire un prélèvement en urgence. Si les valeurs se confirment, mettre le transformateur hors service et planifier une inspection interne.

### R2 — Évaluer l'usure du papier isolant de 4 transformateurs du site CIM

- **Constat :** T2003, T2005, T0404 et T2006 sont en **condition 3**. Leur niveau de gaz vient surtout du CO (1 800 à 2 200 ppm), c'est-à-dire d'un vieillissement avancé du papier isolant, et non d'un défaut électrique (figure 7).
- **Recommandation :** faire une **analyse des furanes**, essai qui mesure directement la dégradation du papier et qui figure déjà dans la base. Selon le résultat, planifier une remise à neuf ou un remplacement.

## 6.3 Actions à court terme

### R3 — Reprendre la surveillance des transformateurs non analysés

- **Constat :** environ **140 transformateurs** n'ont plus fait l'objet d'une analyse de gaz dissous depuis 2000. Leur état actuel est inconnu (figure 7).
- **Recommandation :** les prélever de nouveau, en commençant par les **59 transformateurs en condition 2** (« à surveiller ») à leur dernier prélèvement.

### R4 — Renforcer la surveillance des défauts actifs sur le site CCP

- **Constat :** CCP est le site qui reçoit le plus de recommandations d'action (10 % des prélèvements). On y trouve les transformateurs les plus souvent signalés (T0202 et T0210) et le transformateur critique T0218C (figures 8 et 9).
- **Recommandation :** y suivre en priorité les gaz des défauts actifs (H2, CH4, C2H6, C2H4, C2H2), en rapprochant les prélèvements sur les transformateurs qui ont déjà eu des épisodes de défaut.

### R5 — Adapter la fréquence des prélèvements et suivre la tendance

- **Constat :** les recommandations de l'expert arrivent en série pendant les hausses de gaz, puis s'arrêtent quand les gaz chutent après une intervention. C'est la **tendance** qui compte, plus qu'une valeur isolée (figure 8).
- **Recommandation :** fixer la fréquence des prélèvements de chaque transformateur selon sa condition IEEE C57.104 : plus la condition est élevée, plus les prélèvements sont rapprochés. Comparer aussi chaque résultat au précédent, pour repérer une hausse avant qu'un seuil ne soit dépassé.

## 6.4 Actions à moyen terme

### R6 — Mettre en place une alerte automatique d'aide à la décision

- **Constat :** quand CH4 et C2H4 sont élevés ensemble, une action est recommandée dans **62 %** des cas, contre 7 % en moyenne (étape 4). La forêt aléatoire classe correctement les prélèvements à risque, avec une AUC de **0,82** (étape 3).
- **Recommandation :** signaler automatiquement à l'expert les prélèvements qui :
  - dépassent un seuil IEEE ;
  - présentent la combinaison CH4 + C2H4 élevés ;
  - ont un profil atypique.

  L'alerte sert à **classer les priorités**, pas à remplacer l'avis de l'expert. Le modèle ne retrouve en effet que la moitié des actions.

### R7 — Améliorer la qualité de la saisie des données

- **Constat :** le prétraitement a mis en évidence plusieurs problèmes (étape 2) :
  - des **0** saisis à la place de valeurs inconnues ;
  - **15 analyses saisies en double** ;
  - **3 valeurs d'azote impossibles** ;
  - des noms de fabricants écrits de plusieurs façons ;
  - l'année de fabrication inconnue pour **39 %** des prélèvements et la puissance pour **30 %**.
- **Recommandation :** laisser la case vide quand une valeur est inconnue, ajouter des contrôles à la saisie (doublons, valeurs hors limites physiques, liste fixe de fabricants) et compléter les fiches des équipements (année, puissance).

### R8 — Enregistrer les interventions et les pannes réelles

- **Constat :** la base ne contient **aucune information sur les pannes** ni sur les interventions réalisées. L'étude n'a donc pu prédire que la décision de l'expert, pas la panne elle-même (étape 3).
- **Recommandation :** enregistrer chaque intervention et chaque panne avec sa date, son type et le transformateur concerné. Ces données permettraient de construire un vrai modèle de **maintenance prédictive**, capable d'estimer le risque de panne.

## 6.5 Synthèse

| N° | Recommandation | Priorité |
|---|---|---|
| R1 | Prélèvement d'urgence puis inspection de T0218C (condition 4) | Immédiate |
| R2 | Analyse des furanes pour 4 transformateurs CIM (condition 3) | Immédiate |
| R3 | Nouveaux prélèvements pour les ~140 transformateurs non suivis depuis 2000 | Court terme |
| R4 | Surveillance renforcée des défauts actifs sur le site CCP | Court terme |
| R5 | Fréquence des prélèvements selon la condition IEEE et suivi de la tendance | Court terme |
| R6 | Alerte automatique d'aide à la décision | Moyen terme |
| R7 | Contrôles de qualité à la saisie des données | Moyen terme |
| R8 | Enregistrement des interventions et des pannes | Moyen terme |
