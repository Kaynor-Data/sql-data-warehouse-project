<!--
# sql-data-warehouse-project
Data Warehouse Project : Pipeline ETL complet en SQL (Architecture Medallion) avec implémentation pour PostgreSQL, SQL Server et Oracle.
-->

# 🏢 SQL Data Warehouse Project (Multi-Dialect)

**Projet Data Warehouse complet basé sur l'architecture Medallion (Bronze, Silver, Gold). Implémentation du pipeline ETL en SQL pur pour 3 SGBD : PostgreSQL, SQL Server et Oracle.**

Ce dépôt est un projet complet d'ingénierie de données qui démontre la construction d'un Data Warehouse moderne de bout en bout. En utilisant des fichiers sources bruts (CRM et ERP), ce projet met en œuvre une architecture en médaillon pour nettoyer, transformer et modéliser les données.

La particularité de ce projet est sa **polyvalence** : le pipeline de données a été entièrement développé en SQL pur et est décliné pour trois des systèmes de gestion de bases de données les plus utilisés en entreprise. L'objectif est de démontrer une compréhension profonde des concepts d'entreposage de données, indépendamment de la technologie utilisée.

---

## 🧭 Sommaire / Navigation

Cliquez sur le lien du SGBD qui vous intéresse pour accéder directement à ses scripts et à ses instructions spécifiques :

*   [🐘 **PostgreSQL**](#-postgresql)
*   [🪟 **SQL Server**](#-sql-server)
*   [🔴 **Oracle Database**](#-oracle-database)

---

## 📊 Architecture du Flux de Données

Le projet suit le flux de données illustré ci-dessous :

![Data Flow Diagram](02_docs/data_flow_diagram.png) 
*(Assurez-vous d'ajouter votre image dans le dossier `02_docs`)*

Le pipeline se décompose en trois grandes étapes :
1.  **Source Systems** : Extraction des données depuis les fichiers bruts (CRM et ERP).
2.  **Data Warehouse (Medallion Architecture)** :
    *   🥉 **Bronze** : Ingestion des données brutes (tel quel, sans transformation).
    *   🥈 **Silver** : Nettoyage, standardisation, gestion des valeurs nulles et déduplication.
    *   🥇 **Gold** : Modélisation dimensionnelle (Schéma en étoile) optimisée pour l'analyse et le reporting.
3.  **Consume** : Données prêtes à être consommées par des outils de Business Intelligence (comme Tableau) ou des modèles de Machine Learning.

---

## 🛠️ Implémentations par SGBD

Chaque dossier contient les scripts spécifiques à la syntaxe du SGBD concerné, tout en respectant la même logique d'architecture globale.

### 🐘 PostgreSQL
*   **Dossier** : [`/postgresql`](./postgresql)
*   **Description** : C'est l'implémentation de référence (celle utilisée pour le diagramme).
*   **Exécution** : Ouvrez le dossier `postgresql`, exécutez `00_init_database.sql`, puis suivez l'ordre des dossiers (`01_bronze`, `02_silver`, `03_gold`).

### 🪟 SQL Server
*   **Dossier** : [`/sql_server`](./sql_server)
*   **Description** : Implémentation adaptée pour Microsoft SQL Server (utilisation de `TOP`, `GETDATE()`, types de données spécifiques).
*   **Exécution** : Ouvrez le dossier `sql_server` et exécutez les scripts dans l'ordre chronologique.

### 🔴 Oracle Database
*   **Dossier** : [`/oracle`](./oracle)
*   **Description** : Implémentation adaptée pour Oracle (gestion spécifique des schémas/utilisateurs, utilisation de `SYSDATE`, séquences, etc.).
*   **Exécution** : Ouvrez le dossier `oracle` et exécutez les scripts dans l'ordre.

---

## 📁 Structure du Dépôt

```text
sql-data-warehouse-project/
│
├── README.md                  # Le guide principal avec les liens
├── .gitignore                 # Fichiers à ignorer par Git
│
├── 01_datasets/               # Fichiers sources bruts (CSV) partagés pour tous les SGBD
│   ├── crm_cust_info.csv
│   └── ...
│
├── 02_docs/                   # Documentation et diagrammes
│   └── data_flow_diagram.png
│
├── postgresql/                # 🐘 Implémentation PostgreSQL
│   ├── 00_init_database.sql
│   ├── 01_bronze/
│   ├── 02_silver/
│   └── 03_gold/
│
├── sql_server/                # 🪟 Implémentation SQL Server
│   ├── 00_init_database.sql
│   ├── 01_bronze/
│   ├── 02_silver/
│   └── 03_gold/
│
└── oracle/                    # 🔴 Implémentation Oracle
    ├── 00_init_database.sql
    ├── 01_bronze/
    ├── 02_silver/
    └── 03_gold/
