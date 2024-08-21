## Analyse des Ventes Immobilières en France: Une Étude SQL avec les Données de LaplaceImmo

### Introduction

Ce projet a été réalisé dans le cadre de mon master en Data Analytics à DSTI. En tant que data analyst, j'ai utilisé MySQL pour mener une analyse approfondie des données immobilières de l'agence Laplace Immo, en me concentrant sur diverses dimensions des ventes immobilières, telles que la répartition géographique, les prix au mètre carré, et l'évolution des ventes au fil du temps. L'objectif était de répondre à des questions spécifiques, en utilisant des techniques avancées de requêtage SQL pour extraire et interpréter les informations clés des données disponibles.

### Objectifs du Projet

L'objectif principal de ce projet était d'analyser les tendances du marché immobilier en France en 2020, en répondant aux questions suivantes à l'aide de requêtes MySQL :

1. Réaliser une jointure de 3 tables (Lieu, Bien et Vente)
2. Nombre total d'appartements vendus au 1er semestre 2020
3. Proportion des ventes d'appartements par nombre de pièces
4. Liste des 10 départements où le prix du mètre carré est le plus élevé
5. Prix moyen du mètre carré d'une maison en Île-de-France
6. Liste des 10 appartements les plus chers
7. Taux d'évolution du nombre de ventes entre le premier et le second trimestre 2020
8. Liste des communes où le nombre de ventes a augmenté d'au moins 20% entre le premier et le second trimestre de 2020
9. Différence en pourcentage du prix au mètre carré entre un appartement de 2 pièces et un appartement de 3 pièces
10. Les moyennes de valeurs foncières pour le top 3 des communes des départements 6, 13, 33, 59 et 69

### Démarche et Méthodologie

Dans ce projet, j'ai utilisé les bases de données relationnelles et les requêtes SQL pour extraire, manipuler et analyser les données. La méthodologie adoptée comprend :

- **Exploration des données** : Une première étape d'exploration pour comprendre la structure des données et les relations entre les différentes tables.
- **Jointures SQL** : Application de jointures internes (INNER JOIN) pour combiner les tables et obtenir les données nécessaires pour chaque analyse.
- **Agrégation et Filtrage** : Utilisation de fonctions d'agrégation (COUNT, AVG) et de clauses de filtrage (WHERE, GROUP BY) pour extraire des insights pertinents.
- **Calculs avancés** : Implémentation de sous-requêtes, de CTE (Common Table Expressions), et de fonctions de fenêtre pour des analyses plus complexes, comme les évolutions de tendances et les comparaisons de prix.

### Résultats Clés

- **Volume des Transactions** : Le premier semestre 2020 a vu un nombre significatif de ventes d'appartements, malgré le contexte de la pandémie de COVID-19.
- **Disparités Géographiques** : Les départements avec les prix au mètre carré les plus élevés se concentrent principalement en Île-de-France, reflétant une demande accrue dans cette région.
- **Évolution des Ventes** : Une analyse des tendances de vente a montré une évolution notable entre les trimestres, avec certaines communes enregistrant une augmentation significative des transactions.

### 1. Réaliser une jointure de 3 tables (Lieu, Bien et Vente) :
Intégrer les informations des différentes tables pour obtenir une vue unifiée des données immobilières.

Requête:
```sql
SELECT *
FROM laplace_immo.lieu
JOIN laplace_immo.bien ON laplace_immo.lieu.id_lieu = laplace_immo.bien.id_lieu
JOIN laplace_immo.vente ON laplace_immo.bien.id_bien = laplace_immo.vente.id_bien
```

![](Images/laplace_immo_1.png)
