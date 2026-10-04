# Évolution de l’épidémie à VIH en République du Congo (1990–2025)

Analyse exploratoire de données de santé publique sur le VIH en **République du Congo (COG)**, réalisée avec Python dans le cadre de la formation Data Science d’**Akieni Academy — Semaine 16, Groupe 8**.

Le jeu de données rassemble des séries étiquetées **ONUSIDA** et **OMS**. Les sept indicateurs actuellement visualisés utilisent les séries ONUSIDA sélectionnées dans le fichier, sans cumul des sources ni des populations qui se chevauchent.

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
- [Visualisations](#visualisations)
- [Rapports et documents](#rapports-et-documents)
- [Exécution du projet](#exécution-du-projet)
- [Limites de l’analyse](#limites-de-lanalyse)
- [Membres du groupe](#membres-du-groupe)

---

## Contexte

L’**ONUSIDA** et l’**OMS** publient des données et des estimations sur le VIH afin de décrire la situation épidémiologique, de suivre la riposte et d’éclairer les décisions de santé publique.

Ce projet s’intéresse à l’évolution du VIH en **République du Congo**, distincte de la République démocratique du Congo, entre **1990 et 2025**. Il examine les tendances épidémiologiques, le diagnostic, l’accès au traitement et les différences selon le sexe et l’âge.

---

## Objectif du projet

Constituer, nettoyer et analyser un jeu de données sur le VIH au Congo afin de :

1. Décrire l’évolution de la prévalence, des nouvelles infections et des décès liés au sida.
2. Examiner les indicateurs disponibles de diagnostic et de traitement, en les situant par rapport au cadre **95-95-95**.
3. Décrire les différences selon le **sexe** et les **groupes d’âge disponibles**.
4. Produire des visualisations et des recommandations tenant compte des limites des données.

L’approche est **descriptive** : elle ne démontre pas l’efficacité causale d’une politique publique. L’absence de données sur la suppression virale empêche une évaluation complète de la cascade 95-95-95.

---

## Stratégie mondiale 95-95-95

| Cible | Population de référence et objectif |
|---|---|
| **Premier 95** | 95 % des personnes vivant avec le VIH connaissent leur statut sérologique. |
| **Deuxième 95** | 95 % des personnes vivant avec le VIH connaissant leur statut reçoivent un traitement antirétroviral. |
| **Troisième 95** | 95 % des personnes sous traitement antirétroviral ont une charge virale supprimée. |

**Dans les graphiques actuels :**

- La connaissance du statut est présentée en **nombre de personnes**, et non en pourcentage du premier 95.
- La couverture ARV utilise comme dénominateur **toutes les personnes vivant avec le VIH** : elle ne correspond pas directement au deuxième 95.
- Le troisième 95 **n’est pas calculé**, faute d’indicateur de suppression virale disponible.

Le CSV contient aussi les indicateurs OMS `hiv_statusknownhiv` et `hiv_statusknownart`. Ils ne sont pas utilisés dans les sept séries actuellement tracées et ne doivent pas être confondus avec celles-ci.

---

## Définitions épidémiologiques

| Notion | Définition et utilisation dans le projet |
|---|---|
| **Prévalence du VIH** | Proportion d’une population vivant avec le VIH. Ici : pourcentage chez les **15–49 ans**. |
| **Personnes vivant avec le VIH (PVVIH)** | Effectif estimé de personnes vivant avec le VIH ; à distinguer de la prévalence en pourcentage. |
| **Nouvelles infections** | Nombre estimé d’infections survenues pendant l’année. Le graphique ne représente pas un taux d’incidence rapporté à la population exposée. |
| **Mortalité liée au sida** | Ici : nombre annuel estimé de décès liés au sida, et non taux de mortalité. |
| **Couverture ARV / TARV** | Pourcentage de l’ensemble des PVVIH recevant un traitement antirétroviral. |
| **Connaissance du statut** | Ici : effectif estimé des PVVIH connaissant leur statut, tous âges et deux sexes réunis. |
| **Suppression virale** | Charge virale inférieure au seuil retenu par la définition de l’indicateur ; proportion non calculable dans cette analyse. |

---

## Indicateurs clés (KPIs)

### Indicateurs liés à la stratégie 95-95-95

Le cadre 95-95-95 guide l’interprétation et les recommandations, mais les graphiques de diagnostic et de traitement ne constituent pas une mesure complète de cette cascade. Les dénominateurs doivent toujours être précisés.

### Indicateurs épidémiologiques

- Prévalence du VIH .
- Nombre annuel de nouvelles infections et de décès liés au sida.
- Nombre de PVVIH selon le sexe.
- Nombre de PVVIH dans les groupes **0–14, 15–24 et 15–49 ans**.

### Indicateurs calculés dans le projet

Toutes les lignes ci-dessous utilisent `source_org == 'unaids'`. Les données sont déjà agrégées dans le CSV : le code sélectionne les observations adéquates, contrôle leur unicité et les trie, sans moyenne ni somme supplémentaire.

| Domaine | Code source | Unité | Population retenue | Variable de sortie |
|---|---|---|---|---|
| Prévalence | `hiv_prevalence` | `percent` | `sex == 'both'` | `preva_trend` |
| Connaissance du statut | `number_plhiv_knowledge_of_status` | `count` | Tous âges, `sex == 'both'` | `testing_trend` |
| Couverture ARV | `percent_plwh` | `percent` | Tous âges, `sex == 'both'` | `arv_trend` |
| Nouvelles infections | `new_infections` | `count` | Tous âges, `sex == 'both'` | `incidence_trend` |
| Décès liés au sida | `aids_deaths` | `count` | Tous âges, `sex == 'both'` | `deaths_trend` |
| Répartition par sexe | `plwh` | `count` | femmes et hommes séparément | `gender_trend` |
| Répartition par âge | `plwh` | `count` | 0–14, 15–24, 15–49 ans ; `sex == 'both'` | `age_trend` |

**Attention :** les valeurs en unité `percent` sont déjà exprimées en pourcentage. Il ne faut pas les multiplier à nouveau par 100. Les **15–24 ans sont inclus dans les 15–49 ans** : ces effectifs ne doivent pas être additionnés.

---

## Données

### Source

Les métadonnées du CSV mentionnent l’**ONUSIDA** et l’**OMS**. Ces mentions documentent la provenance déclarée dans le fichier ; elles ne constituent pas une validation indépendante de la collecte auprès des producteurs.

| Élément | Détail |
|---|---|
| Pays | République du Congo (`COG`, normalisé en `cog` après nettoyage) |
| Période globale | 1990–2025 |
| Fichier nettoyé disponible | [`Dataset/Donnees_congo.csv`](Dataset/Donnees_congo.csv) |
| Dimensions du fichier fourni | 1 380 lignes et 12 colonnes |
| Fichier brut appelé par le code | `hiv_global_timeseries.csv` — non inclus dans le dépôt |
| DataFrame de travail | `hiv_cog` |
| Prévalence, nouvelles infections, décès, sexe et âge | 1990–2025 pour les séries retenues |
| Couverture ARV | 2010–2025 |
| Connaissance du statut, en effectif | 2015–2025 |

### Préparation des données

Le notebook et le script effectuent notamment :

- le filtrage du fichier mondial sur le code pays `COG` ;
- la suppression de la colonne `notes` et la conversion de `retrieval_date` ;
- la normalisation des chaînes de caractères ;
- la suppression des doublons exacts ;
- la suppression des lignes contenant des valeurs manquantes avec `dropna()` ;
- l’export du fichier nettoyé et la conversion de l’année en entier.

La suppression générale des valeurs manquantes peut retirer des observations dont seules certaines métadonnées sont absentes. Son effet ne peut pas être entièrement vérifié sans le fichier brut.

Pour l’analyse par âge, les groupes **0–14, 15–24 et 15–49 ans sont conservés** ; la catégorie `all` n’est pas utilisée dans ce graphique.

---

## Méthodologie

Le travail suit une démarche d’**analyse exploratoire et d’épidémiologie descriptive** :

1. Chargement des données et sélection de la République du Congo.
2. Nettoyage et standardisation du jeu de données.
3. Sélection de séries homogènes selon l’indicateur, la source, l’unité, l’âge et le sexe.
4. Vérification que les séries ne sont pas vides et qu’aucune année, ou combinaison année–groupe, n’est dupliquée.
5. Conservation des valeurs sélectionnées, tri chronologique et visualisation.
6. Interprétation des tendances, formulation de recommandations et explicitation des limites.

### Règles de calcul et de sélection

- **Pas de mélange d’unités** : un effectif ne se moyenne pas avec un pourcentage ou un taux.
- **Pas de cumul OMS–ONUSIDA** : deux sources décrivant la même population ne sont pas deux populations à additionner.
- **Pas de double comptage** : les lignes nationales ne sont pas additionnées à leurs sous-groupes.
- **Pas de moyenne simple des couvertures par âge** : la couverture nationale est sélectionnée directement avec `age_group == 'all'` et `sex == 'both'`.
- **Analyse par sexe** : les femmes et les hommes.
- **Analyse par âge** : un seul indicateur, `plwh`, est utilisé, sans lui ajouter les nouvelles infections.
- **Données absentes** : une année manquante ne signifie pas que la valeur est nulle.

---

## Structure du projet

```text
Project_hiv_Congo_Evolution/
├── README.md
├── LICENSE
├── Dataset/
│   └── Donnees_congo.csv          # Données Congo nettoyées
├── Documents/
│   ├── GROUPE 8 PLAN.pdf          # Cadrage et organisation du projet
│   └── Rapport d'EDA VIH.pdf      # Rapport d’analyse au format PDF
├── Scripts/
│   ├── EVAL_GP8.ipynb             # Notebook d’analyse exploratoire
│   └── eval_gp8.py                # Version Python exportée du notebook
├── Rapport d'EDA VIH.md           # Rapport d’analyse au format Markdown
└── resultats.png                  # Tableau de bord multi-indicateurs
```

---

## Visualisations

Le tableau de bord rassemble les sept graphiques suivants :

1. Prévalence du VIH.
2. Nombre de PVVIH connaissant leur statut sérologique.
3. Couverture du traitement ARV parmi toutes les PVVIH.
4. Nombre annuel de nouvelles infections.
5. Nombre annuel de décès liés au sida.
6. Nombre de PVVIH selon le sexe.
7. Nombre de PVVIH par groupe d’âge, avec des catégories non additionnables.

![Analyse multi-indicateurs de l’évolution du VIH au Congo (1990–2025)](resultats.png)


L’interprétation détaillée et les recommandations figurent dans le rapport, plutôt que dans ce document de présentation du projet.

---

## Rapports et documents

- [Rapport d’analyse — Markdown](Rapport%20d%27EDA%20VIH.md)
- [Rapport d’analyse — PDF](Documents/Rapport%20d%27EDA%20VIH.pdf)
- [Plan d’exécution et cadrage du Groupe 8 — PDF](Documents/GROUPE%208%20PLAN.pdf)
- [Notebook d’analyse](Scripts/EVAL_GP8.ipynb)
- [Script Python](Scripts/eval_gp8.py)
- [Graphiques](resultats.png)

---

## Exécution du projet

### Environnement

Utiliser Python avec Jupyter Notebook ou Google Colab. Les bibliothèques importées par le projet sont :

```bash
pip install pandas numpy seaborn matplotlib
```

### Reproduire les calculs à partir du CSV disponible

Pour recalculer les indicateurs sans refaire le nettoyage initial, ouvrir le notebook et exécuter les imports, puis charger le CSV nettoyé dans une cellule dédiée :

```python
import pandas as pd

# Depuis la racine du dépôt :
hiv_cog = pd.read_csv("Dataset/Donnees_congo.csv")
hiv_cog["year"] = hiv_cog["year"].astype(int)
```

Depuis le dossier `Scripts/`, utiliser plutôt `../Dataset/Donnees_congo.csv`. Ensuite, exécuter le bloc **« CRÉATION & CALCUL DES INDICATEURS »**, puis le bloc de visualisation, **sans relancer les cellules de chargement et de nettoyage du fichier brut**.

La version `.py` conserve les appels à `display()` issus du notebook. Pour une exécution Python hors notebook, il faut notamment importer `display` depuis `IPython.display` et adapter le chargement des données ; le script n’est donc pas présenté comme immédiatement exécutable de bout en bout avec le seul CSV nettoyé.

Le code actuel affiche la figure avec `plt.show()` sans l’enregistrer automatiquement. Pour régénérer `resultats.png`, ajouter avant cet appel, depuis la racine du projet :

```python
fig.savefig("resultats.png", dpi=300, bbox_inches="tight")
```

---

## Limites de l’analyse

- **Suppression virale indisponible :** le troisième objectif 95 et les résultats virologiques du traitement ne peuvent pas être évalués.
- **Adolescents non isolables :** les classes disponibles ne permettent pas une analyse spécifique des 10–19 ans.
- **Groupes d’âge imbriqués :** les 15–24 ans appartiennent aux 15–49 ans ; les catégories affichées ne forment pas une partition de la population.
- **Périodes inégales :** les séries de diagnostic et de couverture ARV ne couvrent pas toute la période 1990–2025.
- **Traçabilité :** le fichier brut est absent du dépôt et les métadonnées de source doivent être vérifiées auprès des producteurs, notamment pour préciser le statut des estimations de 2025.
- **Incertitude :** les graphiques ne présentent pas d’intervalles d’incertitude ; les variations récentes doivent être interprétées avec prudence.
- **Causalité :** l’EDA ne démontre ni les causes des disparités ni l’impact causal des politiques de santé.

L’absence de données exploitables ne doit pas être interprétée comme une absence de cas ou de suppression virale.

---

## Membres du groupe

**Akieni Academy — Formation Data Science — Semaine 16 — Groupe 8**

- **MALONGA MBOUITY Giversy Deliche** — Lead
- **M’PIKA Emmanuelle** — Dr
- **M’BIELO-LIBEAU Davis Junior**
- **MANDAKA MARIMPORI Edouarda**
- **AZIDAMA Belvine**

La répartition des tâches et le cadrage du projet sont détaillés dans le [plan d’exécution](Documents/GROUPE%208%20PLAN.pdf).
