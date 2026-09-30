# Évolution de l’infection à VIH en République du Congo (1990–2025)

Analyse de données de santé publique sur le VIH/SIDA en République du Congo, à partir de sources internationales (ONUSIDA, OMS), avec Python et des visualisations.

---

## Table des matières

- [Contexte](#contexte)
- [Objectif du projet](#objectif-du-projet)
- [Stratégie mondiale 95-95-95](#stratégie-mondiale-95-95-95)
- [Définitions épidémiologiques](#définitions-épidémiologiques)
- [Indicateurs clés (KPIs)](#indicateurs-clés-kpis)
- [Données](#données)
- [Méthodologie](#méthodologie)
- [Structure du projet](#structure-du-projet)
- [Installation](#installation)
- [Utilisation](#utilisation)
- [Visualisations](#visualisations)
- [Résultats](#résultats)
- [Technologies](#technologies)
- [Auteurs](#auteurs)

---

## Contexte

L’**ONUSIDA** et l’**OMS** collectent chaque année des données sur plusieurs maladies, notamment le VIH/SIDA, afin de :

- produire des analyses sociodémographiques ;
- décrire la situation épidémiologique ;
- éclairer la décision publique ;
- appuyer les gouvernements dans leur riposte.

Plusieurs pays ont adopté une **stratégie mondiale** sous le lead de l’ONUSIDA, agence des Nations Unies responsable de la riposte au VIH dans le monde.

Ce projet s’intéresse spécifiquement à la **République du Congo (COG)** et à l’évolution de l’infection à VIH sur la période **1990–2025**.

---

## Objectif du projet

Constituer, nettoyer et analyser un jeu de données sur le VIH au Congo, puis calculer des **indicateurs clés de santé publique** afin de :

1. Décrire l’évolution de l’épidémie (prévalence, incidence, mortalité).
2. Mesurer les progrès vers les cibles **95-95-95**.
3. Identifier les disparités selon le **sexe** et l’**âge**.
4. Produire des visualisations claires pour l’aide à la décision.

---

## Stratégie mondiale 95-95-95

La stratégie d’élimination du VIH repose sur trois cibles :

| Cible | Description |
|---|---|
| **95 %** | des personnes infectées par le VIH **connaissent leur statut sérologique** |
| **95 %** | des personnes dépistées positives **sont mises sous TARV** (traitement antirétroviral) |
| **95 %** | des personnes sous TARV **ont une charge virale supprimée** |

Ces trois piliers structurent l’ensemble des indicateurs analysés dans ce projet.

---

## Définitions épidémiologiques

| Notion | Définition |
|---|---|
| **Incidence** | Ensemble des **nouveaux cas** survenus pendant une période donnée |
| **Prévalence** | Ensemble de **tous les cas** (anciens + nouveaux) à un moment ou sur une période donnée |
| **Mortalité liée au SIDA** | Nombre ou taux de décès attribuables au SIDA |
| **Couverture ART / TARV** | Proportion de personnes vivant avec le VIH sous traitement antirétroviral |
| **Connaissance du statut** | Proportion de personnes vivant avec le VIH qui connaissent leur statut sérologique |
| **Suppression virale** | Proportion de personnes sous TARV dont la charge virale est indétectable / supprimée |

---

## Indicateurs clés (KPIs)

### Indicateurs liés à la stratégie 95-95-95

- Pourcentage de personnes positives **connaissant leur statut sérologique**
- Pourcentage de personnes testées positives **mises sous traitement antirétroviral**
- Pourcentage des personnes sous traitement **ayant une charge virale supprimée**

### Indicateurs épidémiologiques

- **Incidence** ou taux de nouvelles infections
- **Prévalence** du VIH dans la population générale
- Prédominance / prévalence selon le **sexe** (hommes vs femmes)
- Nombre / taux de **décès liés au SIDA**
- Répartition selon les **tranches d’âge**

### Indicateurs calculés dans le projet

| Domaine | Indicateurs sources | Méthode | Tendance |
|---|---|---|---|
| Prévalence du VIH | `hiv_0000000001`, `anc_testing`, `hiv_prevalence` | Moyenne annuelle | `testing_trend` |
| Dépistage et statut sérologique | `hiv_statusknownhiv`, `anc_testing`, `number_plhiv_knowledge_of_status` | Moyenne annuelle | `testing_trend` |
| Couverture ART | `percent_plwh`, `hiv_artcoverage` | Moyenne annuelle (`sex == both`) | `art_trend` |
| Nouvelles infections | `new_infections`, `hiv_0000000026` | Somme annuelle | `incidence_trend` |
| Décès liés au SIDA | `aids_deaths`, `hiv_0000000006` | Somme annuelle | `deaths_trend` |
| Répartition par sexe | `hiv_0000000001`, `plwh` | Somme par année et par sexe | `gender_trend` |
| Répartition par âge | `plwh`, `new_infections` | Somme par année et par groupe d’âge | `age_trend` |

---

## Données

### Source

Les données proviennent de collectes annuelles de l’**ONUSIDA** et de l’**OMS**, rassemblées dans un dataset unique.

| Élément | Détail |
|---|---|
| Pays | République du Congo (`COG`) |
| Période | 1990 – 2025 |
| Dataset brut | `hiv_cog` |
| Dataset nettoyé | `Donnees_congo.csv` |

### Préparation des données

Les données ont été traitées pour garantir une base propre et fiable :

- suppression des doublons ;
- traitement des valeurs manquantes ;
- filtrage des indicateurs pertinents ;
- harmonisation des catégories de sexe et d’âge ;
- agrégation par année, sexe ou tranche d’âge.

Pour l’analyse par âge, les groupes agrégés (`all`, `0-14`, `15-24`, `15-49`) ont été exclus afin de conserver une lecture plus granulaire.

---

## Méthodologie

Le travail s’appuie sur une approche de **santé publique / épidémiologie descriptive** :

1. **Collecte** des indicateurs VIH pour le Congo.
2. **Nettoyage** et standardisation du jeu de données.
3. **Création des KPIs** (prévalence, dépistage, ART, incidence, mortalité, sexe, âge).
4. **Agrégation temporelle** (moyenne ou somme annuelle selon la nature de l’indicateur).
5. **Visualisation** des tendances de 1990 à 2025.
6. **Interprétation** au regard des cibles 95-95-95.

### Détail des agrégations

- **Moyenne annuelle** : indicateurs de proportion / couverture (prévalence, dépistage, ART).
- **Somme annuelle** : indicateurs de volume (nouveaux cas, décès).
- **Filtre `sex == 'both'`** : indicateurs globaux (ART, incidence, mortalité).
- **Filtre `male` / `female`** : analyse de genre.
- **Groupes d’âge détaillés** : structure par tranche d’âge.
