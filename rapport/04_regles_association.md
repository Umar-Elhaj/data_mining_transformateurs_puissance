# Étape 4 — Règles d'association

## 4.1 Objectif

Les règles d'association servent à découvrir quels **gaz anormaux apparaissent ensemble** dans un même prélèvement, et quelles combinaisons de gaz conduisent l'expert à **recommander une action**. Les règles sont extraites avec l'algorithme **Apriori** (bibliothèque `mlxtend`).

## 4.2 Préparation des transactions

Apriori travaille sur des transactions. Chaque prélèvement, parmi les 3 870 dont la décision de l'expert est connue, devient un « panier » d'items vrais ou faux :

| Item | Règle | Support |
|---|---|---:|
| CO2 élevé | CO2 > 2 500 ppm | 59,0 % |
| CO élevé | CO > 350 ppm | 57,1 % |
| C2H4 élevé | C2H4 > 50 ppm | 21,3 % |
| C2H2 présent | C2H2 ≥ 2 ppm | 17,2 % |
| H2 élevé | H2 > 100 ppm | 7,8 % |
| Action recommandée | décision de l'expert (étape 3) | 6,7 % |
| C2H6 élevé | C2H6 > 65 ppm | 4,9 % |
| CH4 élevé | CH4 > 120 ppm | 3,9 % |

Les seuils sont ceux de la condition 1 (« normal ») de la norme **IEEE C57.104**, stockés dans la table `NormesC57104` de la base. L'acétylène fait exception : nous avons retenu sa simple présence (≥ 2 ppm), car toute trace est un signal d'alerte. Seuls des items **anormaux** sont créés, pour éviter les règles sans intérêt (« H2 normal → CH4 normal »).

## 4.3 Itemsets fréquents et règles

- **Support minimum de 2 %** : nous obtenons **85 itemsets fréquents**, contenant de 1 à 5 items.
- **Règles retenues** : lift > 1,5 et confiance ≥ 30 %, soit **311 règles**. Pour l'interprétation, nous ne lisons que les règles simples et celles qui mènent à une action.

| Mesure | Définition |
|---|---|
| Support | Part des prélèvements contenant A et B |
| Confiance | Parmi les prélèvements contenant A, part contenant aussi B |
| Lift | Confiance / support de B (> 1 : lien plus fort que le hasard) |

## 4.4 Gaz qui apparaissent ensemble

| Règle | Confiance | Lift |
|---|---:|---:|
| CH4 élevé → C2H6 élevé | 94 % | 19,3 |
| CH4 élevé → H2 élevé | 68 % | 8,7 |
| CH4 élevé → C2H4 élevé | 86 % | 4,0 |
| C2H6 élevé → C2H4 élevé | 75 % | 3,5 |
| C2H4 élevé → CO élevé | 91 % | 1,6 |

Les hydrocarbures (CH4, C2H6, C2H4) et l'hydrogène sont produits **ensemble** : c'est la signature d'une **surchauffe de l'huile**, qui s'étend souvent au papier isolant (CO). Le lien avec l'acétylène est plus faible (lift ≤ 2,5) : l'arc électrique est un défaut à part. Apriori retrouve ainsi, sans connaissance préalable, les familles de gaz connues des experts.

## 4.5 Combinaisons qui mènent à une action

Sans condition, une action est recommandée dans **6,7 %** des cas.

| Si… | Action recommandée | Lift |
|---|---:|---:|
| CH4 élevé + C2H4 élevé | 62 % | 9,3 |
| CH4 élevé | 59 % | 8,8 |
| C2H6 élevé | 50 % | 7,5 |
| C2H2 présent + CO élevé + CO2 élevé | 35 % | 5,1 |
| H2 élevé | 31 % | 4,6 |

Ce sont les **surchauffes de l'huile** (CH4, C2H6, C2H4) qui déclenchent le plus souvent une action. Ce résultat est cohérent avec l'importance des gaz trouvée à l'étape 3. Même la meilleure règle reste à 62 % de confiance : l'expert tient aussi compte d'autres éléments, comme l'évolution des gaz dans le temps.

## 4.6 Bilan

| Élément | Résultat |
|---|---|
| Gaz associés | CH4 ↔ C2H6 (lift 19), avec H2 et C2H4 : signature de la surchauffe |
| Règle la plus fiable vers l'action | CH4 élevé + C2H4 élevé → action dans 62 % des cas |

**Limite :** les supports sont faibles (2 à 4 %). Ces situations sont rares, mais ce sont précisément celles qui comptent pour la maintenance.
