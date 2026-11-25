# 🏢 SQL Data Warehouse Project
## Construction d'un Data Warehouse avec SQL – Architecture Medallion

![SQL](https://img.shields.io/badge/SQL-Data%20Engineering-blue)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Implementation-4169E1)
![SQL Server](https://img.shields.io/badge/SQL%20Server-Implementation-CC2927)
![Oracle](https://img.shields.io/badge/Oracle-Implementation-F80000)
![Status](https://img.shields.io/badge/Status-In%20Progress-orange)

---

## 1. À propos du projet

Ce projet consiste à concevoir et construire un **Data Warehouse de bout en bout**, en utilisant principalement SQL et en appliquant l'architecture Medallion (Bronze, Silver, Gold).

L'objectif est de comprendre concrètement comment les données provenant de différents systèmes opérationnels, notamment CRM et ERP, peuvent être collectées, intégrées, nettoyées, transformées et organisées pour répondre aux besoins analytiques d'une entreprise.

Ce projet est également une démarche personnelle d'apprentissage en Data Engineering.

Plutôt que de reproduire simplement les étapes d'un tutoriel, chaque phase est accompagnée d'explications techniques, de scripts SQL, de vérifications et de notes permettant de comprendre les choix effectués.

### Objectifs pédagogiques

À travers ce projet, je souhaite maîtriser progressivement :

- La conception et l'organisation d'un Data Warehouse.
- Les différences entre une base de données transactionnelle (OLTP) et un Data Warehouse (OLAP).
- Les différentes étapes d'un processus ETL.
- L'architecture Medallion : Bronze, Silver et Gold.
- L'importation de données provenant de fichiers CSV.
- Le nettoyage et la standardisation des données avec SQL.
- La gestion des doublons et des valeurs manquantes.
- La modélisation dimensionnelle.
- La conception d'un schéma en étoile (Star Schema).
- La création de tables de faits et de dimensions.
- L'optimisation des requêtes SQL.
- La préparation des données pour les outils de Business Intelligence.

---

## 2. Comprendre le fonctionnement d'un Data Warehouse

Avant de construire le projet, il est important de comprendre le problème auquel répond un Data Warehouse.

Dans une entreprise, les données sont généralement réparties dans plusieurs systèmes :

- CRM : gestion des clients et des relations commerciales.
- ERP : gestion des commandes, des produits, des stocks et des opérations.
- Autres applications : fichiers Excel, CSV, applications métier et bases de données.

Ces systèmes sont principalement conçus pour enregistrer et gérer les opérations quotidiennes.

Cependant, lorsqu'une entreprise souhaite répondre à des questions comme :

- Quel est le chiffre d'affaires réalisé chaque mois ?
- Quels sont les produits les plus vendus ?
- Quels clients génèrent le plus de revenus ?
- Comment les ventes évoluent-elles dans le temps ?

Il devient nécessaire de regrouper et d'organiser les données provenant de différentes sources.

C'est le rôle du Data Warehouse.

### OLTP vs OLAP

| Critère | OLTP | OLAP |
|---|---|---|
| Objectif | Gérer les opérations | Analyser les données |
| Utilisation | Applications métier | Reporting et BI |
| Données | Opérationnelles | Historiques et consolidées |
| Requêtes | INSERT, UPDATE, DELETE | SELECT, JOIN, GROUP BY |
| Exemple | Enregistrer une commande | Analyser les ventes |

**À retenir :** les bases OLTP permettent à l'entreprise de fonctionner au quotidien, tandis que les systèmes OLAP permettent d'exploiter les données pour prendre des décisions.

---

## 3. Architecture globale du projet

Le projet repose sur trois grandes étapes :

1. Les systèmes sources.
2. Le Data Warehouse organisé selon l'architecture Medallion.
3. La consommation des données.

### Flux de données

```text
                  SOURCE SYSTEMS
                 ┌───────────────┐
                 │               │
                 │      CRM      │
                 │               │
                 └───────┬───────┘
                         │
                         │
                 ┌───────▼───────┐
                 │               │
                 │      ERP      │
                 │               │
                 └───────┬───────┘
                         │
                         ▼
                ┌─────────────────┐
                │      BRONZE     │
                │                 │
                │ Raw Data        │
                │ Ingestion       │
                └────────┬────────┘
                         │
                         ▼
                ┌─────────────────┐
                │      SILVER     │
                │                 │
                │ Data Cleaning   │
                │ Standardization │
                │ Deduplication   │
                └────────┬────────┘
                         │
                         ▼
                ┌─────────────────┐
                │       GOLD      │
                │                 │
                │ Fact Tables     │
                │ Dimensions      │
                │ Star Schema     │
                └────────┬────────┘
                         │
                         ▼
                ┌─────────────────┐
                │   CONSUMPTION   │
                │                 │
                │ Power BI        │
                │ Metabase        │
                │ Analytics       │
                └─────────────────┘
```

### 3.1. Les systèmes sources

Les données utilisées proviennent de fichiers CRM et ERP.

Ces fichiers représentent des données issues de systèmes opérationnels.

Ils constituent le point de départ du processus ETL.

### 3.2. Bronze Layer – Données brutes

La couche Bronze est la première zone de stockage du Data Warehouse.

Elle reçoit les données provenant des systèmes sources.

**Son rôle :**
- Importer les données.
- Conserver les données dans leur état initial autant que possible.
- Garder une représentation fidèle des données sources.
- Faciliter les contrôles et les reprises en cas de problème.

Pourquoi ne pas nettoyer immédiatement les données ?

Parce que conserver une copie brute permet de revenir aux données initiales en cas d'erreur pendant les transformations.

### 3.3. Silver Layer – Données nettoyées

La couche Silver contient les données après les premières transformations.

**Son rôle :**
- Nettoyer les données.
- Traiter les valeurs NULL.
- Supprimer ou gérer les doublons.
- Standardiser les formats.
- Corriger les incohérences.
- Harmoniser les informations provenant de différentes sources.

Exemple :

Dans une source, un client peut être enregistré avec le nom `KONATE`, tandis qu'une autre ligne contient ` Konate `.

La couche Silver permet d'appliquer des règles de standardisation afin d'obtenir des données cohérentes.

### 3.4. Gold Layer – Données analytiques

La couche Gold contient les données organisées pour les analyses et les besoins métier.

Elle repose ici sur la modélisation dimensionnelle.

On y retrouve notamment :

- Les tables de faits (Fact Tables).
- Les tables de dimensions (Dimension Tables).
- Les relations entre les différentes tables.

Exemple de modélisation :

```text
                 DIM_CUSTOMERS
                       |
                       |
DIM_PRODUCTS --- FACT_SALES --- DIM_DATES
                       |
                       |
                 DIM_LOCATIONS
```

La table de faits contient les mesures numériques et les clés permettant de retrouver les informations descriptives dans les dimensions.

Cette organisation facilite les analyses et les rapports décisionnels.

---

## 4. Comprendre le processus ETL

ETL signifie :

- **Extract** : extraire les données depuis les systèmes sources.
- **Transform** : transformer et nettoyer les données.
- **Load** : charger les données dans leur destination.

Dans ce projet, les différentes opérations sont réalisées principalement avec SQL.

### Extract – Extraction

Les fichiers CSV contenant les données CRM et ERP sont récupérés pour être intégrés dans la base de données.

### Load – Chargement initial

Les données sont importées dans les tables Bronze.

### Transform – Transformation

Les données sont nettoyées et préparées dans Silver, puis organisées dans Gold.

**À retenir :** l'architecture Medallion décrit l'organisation et les niveaux de qualité des données, tandis que l'ETL décrit le processus d'extraction, de transformation et de chargement.

Les deux notions sont complémentaires.

---

## 5. Technologies et outils utilisés

| Technologie | Utilisation |
|---|---|
| PostgreSQL | Implémentation du Data Warehouse |
| SQL Server | Implémentation complémentaire |
| Oracle Database | Implémentation complémentaire |
| DBeaver | Administration et exécution SQL |
| SQL | Création et transformation des données |
| Draw.io | Modélisation et diagrammes |
| Git / GitHub | Versionnement et documentation |

### Pourquoi utiliser plusieurs SGBD ?

L'objectif est de comprendre que les principes du Data Warehouse restent globalement les mêmes, même si les syntaxes SQL et certaines fonctionnalités diffèrent selon les moteurs.

Par exemple :

| Besoin | PostgreSQL | SQL Server | Oracle |
|---|---|---|---|
| Date actuelle | CURRENT_DATE | GETDATE() | SYSDATE |
| Limiter les résultats | LIMIT | TOP | FETCH FIRST |
| Valeurs générées | IDENTITY | IDENTITY | IDENTITY / SEQUENCE |

Il ne s'agit pas simplement de copier les mêmes scripts dans trois dossiers, mais de comprendre comment adapter une logique de traitement à chaque SGBD.

---

## 6. Structure du dépôt

```text
sql-data-warehouse-project/
│
├── README.md
├── .gitignore
│
├── 01_datasets/
│   ├── crm_cust_info.csv
│   ├── crm_prd_info.csv
│   ├── crm_sales_details.csv
│   └── ...
│
├── 02_docs/
│   ├── data_flow_diagram.png
│   ├── data_architecture.png
│   └── notes_apprentissage.md
│
├── postgresql/
│   ├── 00_init_database.sql
│   │
│   ├── 01_bronze/
│   │   ├── ddl_bronze.sql
│   │   ├── proc_load_bronze.sql
│   │   └── ...
│   │
│   ├── 02_silver/
│   │   ├── ddl_silver.sql
│   │   ├── proc_load_silver.sql
│   │   └── ...
│   │
│   └── 03_gold/
│       ├── ddl_gold.sql
│       ├── dim_customers.sql
│       ├── dim_products.sql
│       ├── fact_sales.sql
│       └── ...
│
├── sql_server/
│   ├── 00_init_database.sql
│   ├── 01_bronze/
│   ├── 02_silver/
│   └── 03_gold/
│
└── oracle/
    ├── 00_init_database.sql
    ├── 01_bronze/
    ├── 02_silver/
    └── 03_gold/
```

Chaque dossier possède une responsabilité précise.

Les scripts sont organisés de manière à faciliter leur exécution, leur compréhension et leur maintenance.

---

## 7. Implémentation PostgreSQL

PostgreSQL constitue l'implémentation de référence de ce projet.

C'est sur cette version que les concepts sont étudiés et validés avant leur adaptation aux autres SGBD.

### Étape 1 – Initialisation

Créer la base de données et les schémas nécessaires.

Exemple :

```sql
CREATE DATABASE datawarehouse;
```

Les schémas permettent de séparer logiquement les différentes couches du Data Warehouse.

```sql
CREATE SCHEMA bronze;
CREATE SCHEMA silver;
CREATE SCHEMA gold;
```

**Pourquoi créer des schémas ?**

Pour organiser les tables selon leur responsabilité et éviter de mélanger les données brutes, nettoyées et analytiques.

### Étape 2 – Création des tables Bronze

Créer les tables destinées à recevoir les fichiers sources.

Les colonnes doivent respecter la structure des fichiers importés.

### Étape 3 – Importation des données

Importer les fichiers CSV dans les tables Bronze.

L'importation doit respecter :
- Le séparateur utilisé.
- L'encodage du fichier.
- L'ordre des colonnes.
- Les formats de dates.
- Les types de données.

### Étape 4 – Transformation Silver

Appliquer les règles de nettoyage et de standardisation.

Exemples de fonctions SQL à étudier :

```sql
TRIM()
UPPER()
LOWER()
COALESCE()
NULLIF()
ROW_NUMBER()
CAST()
```

### Étape 5 – Modélisation Gold

Créer les tables de dimensions et de faits.

Étudier :
- Les clés primaires.
- Les clés étrangères.
- Les clés substituts (Surrogate Keys).
- Les mesures.
- La granularité d'une table de faits.
- Les relations entre dimensions et faits.

---

## 8. Comment exécuter le projet ?

### Prérequis

Avant de commencer, installer :

- PostgreSQL.
- DBeaver.
- Git.
- Un éditeur de texte ou un IDE.

### Ordre d'exécution

L'exécution doit respecter l'ordre des différentes couches.

| Ordre | Étape | Objectif |
|---|---|---|
| 1 | Initialisation | Créer la base et les schémas |
| 2 | Bronze DDL | Créer les tables brutes |
| 3 | Bronze Load | Importer les CSV |
| 4 | Silver DDL | Créer les tables nettoyées |
| 5 | Silver Load | Nettoyer et transformer |
| 6 | Gold DDL | Créer les tables analytiques |
| 7 | Gold Load | Alimenter les dimensions et les faits |
| 8 | Validation | Vérifier les données et les relations |

Chaque étape doit être contrôlée avant de passer à la suivante.

---

## 9. Contrôles et validation des données

Un pipeline ETL ne doit pas uniquement charger des données : il doit également garantir leur cohérence.

Quelques contrôles à effectuer :

### Vérifier le nombre de lignes

```sql
SELECT COUNT(*)
FROM bronze.crm_cust_info;
```

### Identifier les valeurs NULL

```sql
SELECT *
FROM silver.crm_cust_info
WHERE cst_id IS NULL;
```

### Identifier les doublons

```sql
SELECT
    cst_id,
    COUNT(*)
FROM silver.crm_cust_info
GROUP BY cst_id
HAVING COUNT(*) > 1;
```

### Vérifier les données analytiques

```sql
SELECT
    COUNT(*) AS nombre_ventes,
    SUM(sales_amount) AS chiffre_affaires
FROM gold.fact_sales;
```

Ces contrôles permettent de vérifier que les transformations ont produit les résultats attendus.

---

## 10. Notions SQL à maîtriser pendant le projet

Ce projet constitue également un support de révision et de progression en SQL.

| Famille | Concepts |
|---|---|
| DDL | CREATE, ALTER, DROP, TRUNCATE |
| DML | INSERT, UPDATE, DELETE |
| DQL | SELECT, JOIN, GROUP BY, HAVING |
| DCL | GRANT, REVOKE |
| TCL | COMMIT, ROLLBACK, SAVEPOINT |

Autres notions importantes :

- CTE et sous-requêtes.
- Fonctions de fenêtrage.
- Index et performances.
- Vues et vues matérialisées.
- Procédures stockées.
- Transactions.
- Contraintes d'intégrité.
- Gestion des erreurs.

L'objectif n'est pas de simplement connaître la syntaxe, mais de comprendre dans quelles situations chaque notion est utilisée dans un Data Warehouse.

---

## 11. Contexte professionnel et évolutions possibles

Une fois le pipeline SQL maîtrisé, plusieurs évolutions permettront de rapprocher le projet d'une architecture Data Engineering utilisée en entreprise.

### Évolution 1 – Automatisation

Ajouter Python ou Apache Airflow pour automatiser l'exécution des différentes étapes.

### Évolution 2 – Orchestration

Mettre en place des workflows permettant de gérer :
- L'ordre d'exécution des traitements.
- Les dépendances entre les tâches.
- Les échecs et les nouvelles tentatives.
- La planification des chargements.

### Évolution 3 – Scalabilité avec Citus

Étudier PostgreSQL avec Citus pour comprendre comment distribuer les données et les requêtes sur plusieurs nœuds.

Cette évolution permettra d'aborder :
- La distribution des tables.
- Les clés de distribution.
- Les shards.
- Les requêtes distribuées.
- Les performances et les limites d'une architecture distribuée.

### Évolution 4 – Business Intelligence

Connecter le Data Warehouse à Metabase ou Grafana pour construire des tableaux de bord analytiques.

### Évolution 5 – Qualité et gouvernance

Ajouter progressivement :
- Des contrôles automatisés de qualité.
- La traçabilité des traitements.
- La documentation des tables.
- Le suivi des exécutions.
- Le catalogage des données.

---

## 12. Journal d'apprentissage

Cette section est destinée à conserver une trace de mon évolution et des difficultés rencontrées.

Pour chaque nouvelle notion, je cherche à répondre à quatre questions :

1. Qu'est-ce que c'est ?
2. Pourquoi est-ce utilisé ?
3. Comment fonctionne-t-il techniquement ?
4. Dans quel contexte professionnel peut-on l'utiliser ?

### Exemple de fiche de connaissance

**Notion : Architecture Medallion**

- Définition : organisation des données en plusieurs couches de traitement.
- Pourquoi : améliorer progressivement la qualité et l'organisation des données.
- Application : Bronze pour les données brutes, Silver pour les données nettoyées et Gold pour les données analytiques.
- Ce que je dois retenir : chaque couche a une responsabilité précise et les transformations doivent être maîtrisées.

### Suivi de progression

- [ ] Comprendre les concepts OLTP et OLAP.
- [ ] Comprendre le fonctionnement d'un Data Warehouse.
- [ ] Comprendre l'architecture Medallion.
- [ ] Créer la base et les schémas PostgreSQL.
- [ ] Importer les données CSV.
- [ ] Construire la couche Bronze.
- [ ] Construire la couche Silver.
- [ ] Construire la couche Gold.
- [ ] Comprendre la modélisation dimensionnelle.
- [ ] Construire un schéma en étoile.
- [ ] Effectuer les contrôles de qualité.
- [ ] Adapter le projet à SQL Server.
- [ ] Adapter le projet à Oracle.
- [ ] Automatiser le pipeline.
- [ ] Étudier la distribution avec Citus.

---

## 13. Ressources et documentation

Documentation officielle :

- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Microsoft SQL Server Documentation](https://learn.microsoft.com/en-us/sql/sql-server/)
- [Oracle Database Documentation](https://docs.oracle.com/en/database/)
- [DBeaver Documentation](https://dbeaver.com/docs/)
- [Apache Airflow Documentation](https://airflow.apache.org/docs/)
- [Citus Documentation](https://docs.citusdata.com/)

---

## 14. À propos de moi

Je suis en formation et en progression dans le domaine du Data Engineering et de la Data Science.

À travers ce projet, mon objectif est de développer des compétences techniques solides en conception de Data Warehouse, en SQL, en intégration de données et en architecture des systèmes de données.

Je privilégie une approche pratique et documentée : comprendre les concepts, expérimenter, résoudre les problèmes, puis conserver une trace technique de chaque apprentissage.

**GitHub :** [Kaynor-Data](https://github.com/Kaynor-Data)

---

*Ce projet évoluera progressivement au fur et à mesure de mes apprentissages, expérimentations et améliorations techniques.*
