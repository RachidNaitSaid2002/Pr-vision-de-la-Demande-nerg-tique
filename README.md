# Pr-vision-de-la-Demande-nerg-tique

Ce projet analyse la demande et la production énergétique. Voici les conclusions tirées de chaque étape du notebook :

## Notebook Conclusions:

### 1. Environnement et Chargement
* **Import des bibliothèques** : Utilisation de `pandas`, `matplotlib` et `seaborn` pour l'analyse et la visualisation.
* **Chargement des données** : Les données sont chargées depuis `Data.csv`, contenant les colonnes de consommation, production et les différentes sources d'énergie.
* **Exploration initiale** : Le jeu de données contient initialement 54 170 entrées.

### 2. Nettoyage et Préparation
* **Valeurs nulles** : Aucune valeur manquante n'a été détectée dans le dataset.
* **Doublons** : 4 lignes en double ont été identifiées et supprimées pour garantir l'intégrité de l'analyse.
* **Formatage temporel** : La colonne `DateTime` a été convertie en objets datetime et les données ont été triées chronologiquement.
* **Ingénierie de variables** : Extraction de l'année, du mois, du jour et de l'heure pour faciliter l'analyse temporelle.

### 3. Analyse de la Consommation
* **Profil horaire** : La consommation moyenne varie selon l'heure de la journée.
* **Heure de pointe** : Le pic de consommation a été identifié à **20h00**.

### 4. Bilan Énergétique
* **Balance Production/Consommation** : Sur l'ensemble de la période, la consommation totale (353 518 278 MWh) est supérieure à la production totale (345 593 952 MWh).
* **Analyse des sources** : Le nucléaire est le principal contributeur à la production d'énergie, suivi par l'hydroélectrique et l'éolien.
* **Déficit de production** : Une analyse a permis d'identifier les heures où la production est insuffisante pour couvrir la demande, confirmant une dépendance aux importations ou au stockage pendant ces périodes.

## Notebook1 Conclusions:
### 1. Pourquoi l’ordre temporel est critique
Dans les séries temporelles, le temps n'est pas une donnée ordinaire, c'est une information structurelle. Par exemple, il est possible de prédire la valeur à 12h en utilisant les données de 10h et 11h. Le modèle apprend ainsi la tendance (trend) et la saisonnalité (seasonality).

### 2. Pourquoi la normalisation est indispensable en Deep Learning
La normalisation met les données sur la même échelle. Elle aide le modèle à apprendre plus vite, rend l’entraînement plus stable et améliore la performance du réseau.

### 3. Pourquoi MinMax est souvent préféré à StandardScaler pour LSTM
MinMax met les données entre 0 et 1, ce qui convient bien aux LSTM car ils utilisent des fonctions d’activation comme sigmoid et tanh. StandardScaler peut produire des valeurs trop grandes, ce qui peut rendre l’apprentissage plus difficile pour les LSTM.

### 4. Pourquoi on ne mélange pas les données (pas de shuffle)
En Machine Learning classique, on utilise train_test_split avec un mélange aléatoire (shuffle). Mais dans les séries temporelles, c'est une catastrophe : si on mélange les données, on peut se retrouver avec des données de 2023 dans l'entraînement et des données de 2021 dans le test. Le modèle s'entraîne alors sur le futur pour prédire le passé, ce qui est impossible en conditions réelles.

### 5.  Pourquoi un LSTM attend des données en 3D et Signification de la forme : (samples, timesteps, features)
Contrairement à la Régression Linéaire ou au Random Forest, le LSTM est conçu pour les séquences. Il nécessite des données structurées selon 3 dimensions :

    Samples : Le nombre d'exemples d'entraînement (ex: 10 000).

    Timesteps : La longueur de la fenêtre temporelle (ex: 24 heures pour regarder un jour en arrière).

    Features : Le nombre de variables par pas de temps (ex: 7 sources d'énergie).

    La forme de l'entrée est donc (Samples, Timesteps, Features).

1. Mémoire interne : Le LSTM possède des "portes" (gates) qui décident quelle information de l'Heure -24 est encore importante lorsqu'il arrive à l'Heure -1.
2. Relations multi-variables : Il ne regarde pas seulement le solaire ou l'éolien séparément, mais comment les 7 énergies interagissent entre elles sur une durée de 24h.

### 6. Pourquoi on ne fait pas de traintestsplit classique
On ne fait pas un train-test split classique parce que, dans les séries temporelles, l’ordre du temps est important. Mélanger les données ferait apprendre au modèle des informations du futur, ce qui fausse les résultats.

### 7. Risque de fuite de données temporelles
La fuite de données temporelles se produit quand le modèle utilise des informations du futur pour l’entraînement. Cela donne de très bons résultats artificiels, mais le modèle échoue en conditions réelles car il n’a pas appris correctement.

### 8. Impact du nombre d’unités LSTM
Le nombre d’unités LSTM détermine la capacité du modèle. Trop peu d’unités → modèle trop simple. Trop d’unités → risque d’overfitting et temps d’entraînement plus long.

### 9. Différence entre 1 ou plusieurs couches LSTM
Une seule couche LSTM apprend des relations simples. Plusieurs couches LSTM permettent de capter des relations plus complexes, mais augmentent le risque d’overfitting et le coût de calcul.
