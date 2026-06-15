Feuille de route – “Stripe Business Case”  
Objectif : produire,  les livrables demandés (architecture, modèles de données, pipeline, sécurité, ML, requêtes) tout en couvrant les critères d’évaluation.
• Concevoir une base « transactionnelle » (OLTP) pour les paiements.
• Concevoir un entrepôt de données « analytique » (OLAP) pour faire des rapports.
• Concevoir une base NoSQL (documents/logs) pour la fraude et le comportement client.
• Relier tout ça via un pipeline de données sûr et conforme (sécurité, RGPD, PCI).
────────────────────────
1. Lecture & cadrage (15 min)
• Parcourir les exigences Business / Techniques + critères d’évaluation.  
• Lister rapidement les livrables :  
  1) Architecture diagramme global  
  2) ERD OLTP  
  3) Schéma OLAP (star/snowflake)  
  4) Modèle NoSQL  
  5) Doc pipeline (batch + streaming)  
  6) Plan sécurité / conformité  
  7) Stratégie ML  
  8) Jeu de requêtes SQL / NoSQL  
• Choisir stack :  
  - OLTP : PostgreSQL (ou Aurora PG)  
  - OLAP : Snowflake / BigQuery / Redshift Spectrum  
  - NoSQL : MongoDB (document) + S3/Lake pour logs  
  - Streaming : Airflow + Airflow Connect + Debezium (CDC)  
  - Orchestration : Airflow  
  - Infra : Kubernetes ou managed services (EKS/GKE)  

2. Esquisse architecture haute-niveau (25 min)
• Dessiner un “layered” diagramme (draw.io, Lucidchart) :  
  - Ingestion layer (API Gateway → OLTP; Airflow → Lake)  
  - Change Data Capture (Debezium) from OLTP → Airflow topics  
  - Stream processing (Airflow Streams / Flink) →  
        a) real-time fraud features → MongoDB → ML service  
        b) near-real-time aggregates → OLAP (Snowpipe, Kinesis Firehose, etc.)  
  - Batch layer (Airflow) → ETL/ELT → OLAP + feature store  
  - Data Lake (S3) as single source of truth; versioned parquet/iceberg tables  
  - Serving layer (Looker / Tableau / internal APIs)  
• Annoter sécurité (encryption, RBAC, VPC peering).  

3. Modèle OLTP (20 min)
• Normalisation 3NF :  
  Merchants, Customers, Payment_Methods, Transactions, Chargebacks, Subscriptions, Refunds, Currencies, Devices, Locations, Fraud_Scores.  
• Clés primaires (UUID), index sur FK + champs de filtrage (merchant_id, created_at).  
• Contraintes : FK on delete restrict, unique indexes, check constraints (amount > 0).  
• Haute dispo : logical replication, multi-AZ, failover.  
• Ajouter table outbox (event sourcing) pour CDC.

4. Modèle OLAP (20 min)
• Star schema centré sur FACT_TRANSACTIONS (PK txn_id surrogate, FK vers dimensions)  
  Dimensions : DIM_MERCHANT, DIM_CUSTOMER, DIM_DATE, DIM_PAYMENT_METHOD, DIM_DEVICE, DIM_GEO, DIM_FRAUD_SCORE_BUCKET.  
• SCD II sur marchands & clients (historisation).  
• Pré-agrégations :  
  - daily_merchant_revenue, weekly_product_churn etc. en matérialisé.  
• Partitioning : date → clustering par merchant_id.  
• Prise en charge time-series → window functions + materialized views.

5. Modèle NoSQL (15 min)
• MongoDB collections :  
  a) session_events {session_id, ts, user_id, actions:[…], device,…} (schema flexible).  
  b) fraud_features {txn_id, features:{…}, label}.  
  - Embedding quand 1:N serré (actions dans un session_event).  
  - Referencing vers transactions (store txn_id).  
• Index TTL sur logs, index composés sur {user_id, ts}.  
• Data Lake Raw Logs (S3/Parquet) + Iceberg table for analytics.

6. Pipeline de données (20 min)
Batch :  
  - Airflow DAG : extract S3 → staging → Snowflake COPY; transform (dbt) → marts.  
Streaming :  
  - Debezium captures OLTP changes → Airflow topics.  
  - Airflow Streams / Flink does fraud-feature calc → writes MongoDB + Airflow sink to Snowflake (Snowpipe Auto-ingest).  
  - Schema Registry (Avro/JSON schema).  
Sync/consistency :  
  - Idempotent upserts, event time ordering, “exactly-once” semantics in Streams.  
  - CDC lag monitoring (Prometheus/Grafana).  

7. Sécurité & conformité (15 min)
• Chiffrement : TLS en transit, AES-256 at rest (KMS).  
• RBAC : IAM policies + row-level security (Snowflake).  
• Tokenisation / vault pour PAN données (PCI-DSS).  
• Auditing : CloudTrail + database audit logs, centralized in SIEM.  
• GDPR : data catalog + Data Subject Request API (delete/anonymise).  
• Automated compliance runbooks (Airflow tasks) → evidence storage.  

8. Intégration Machine Learning (15 min)
• Feature store = MongoDB + Snowflake table; versioned features.  
• Real-time service : gRPC model endpoint (SageMaker / Vertex) consumes fraud_features, returns risk_score into Transactions table.  
• Model registry (MLflow) + CI/CD pipeline for retraining (Airflow schedule).  
• Concept-drift monitoring : dashboards on KS statistics.  

9. Jeu de requêtes (10 min)
SQL Snowflake :  
  -- Revenu mensuel par marchand  
  SELECT merchant_id, DATE_TRUNC('month', txn_date) mth, SUM(amount) revenue  
  FROM FACT_TRANSACTIONS  
  WHERE status='successful'  
  GROUP BY 1,2  
  ORDER BY 2 DESC;  

MongoDB :  
  -- Sessions suspectes (>20 actions/min)  
  db.session_events.aggregate([  
    {$group:{_id:"$session_id", cps:{$avg:{$divide:[{$size:"$actions"},60]}}}},  
    {$match:{cps:{$gt:20}}}  
  ])  

10. Production des supports (diagrammes + doc) (15 min)
• Export diagrammes PNG / PDF.  
• Compiler un document (Google Docs ou Markdown → PDF) avec sections alignées aux livrables.  
• Ajouter table de correspondance critères ↔ sections.

11. Relecture & check-list (10 min)
• Vérifier chaque critère (1.x, 2.x, …) marqué ✓.  
• Orthographe + cohérence nommage.  
• Sauvegarde & livraison.

────────────────────────
Conseils pratiques
• Réutilise des modèles Draw.io “Cloud Architecture” pour gagner du temps.  
• dbdiagram.io / SQLDBM → ERD rapide, export PNG.  
• Utilise la nomenclature anglaise pour les schémas, français pour le texte si consigne bilingue.  
• Time-box : si une section dépasse, revenir plus tard – mieux vaut une version “80 % partout” qu’un seul livrable parfait.  
• Mets des couleurs pour distinguer OLTP (bleu), OLAP (vert), NoSQL (orange) sur le diagramme.  
• Dans le code/queries : respecter PEP-8, commenter, gérer exceptions (critère 5.x).

-----
Pas de panique ! Repartons de zéro, avec un langage très simple et un pas-à-pas ultra-clair.  
Le but du test est de montrer que tu sais :

• Concevoir une base « transactionnelle » (OLTP) pour les paiements.  
• Concevoir un entrepôt de données « analytique » (OLAP) pour faire des rapports.  
• Concevoir une base NoSQL (documents/logs) pour la fraude et le comportement client.  
• Relier tout ça via un pipeline de données sûr et conforme (sécurité, RGPD, PCI).  

En 180 minutes on ne te demande pas de coder l’usine à gaz ; on veut un dossier/diapo + quelques requêtes de démonstration.  

────────────────────────
I. QUE DOIS-TU RENDRE ?  
1. Un schéma global (un dessin) montrant comment les trois bases s’enchaînent.  
2. Un petit diagramme (ERD) pour la base OLTP.  
3. Un schéma en étoile (star) pour l’OLAP.  
4. Un exemple de structure de documents pour la base NoSQL.  
5. ½ page expliquant le pipeline (batch + temps réel).  
6. ½ page expliquant la sécurité/conformité.  
7. ½ page expliquant où rentrent les modèles de machine-learning.  
8. 4-5 requêtes SQL / Mongo pour prouver que tes schémas servent bien.  

────────────────────────
II. COMMENT T’Y PRENDRE (plan de travail)  

Étape 1 – Choisir tes outils  
• OLTP : PostgreSQL (classique, ACID).  
• OLAP : Snowflake (ou BigQuery).  
• NoSQL : MongoDB (documents).  
• Streaming : Airflow (messages en temps réel).  
• Orchestration batch : Airflow.  


Étape 2 – Dessiner l’architecture 
Ouvre draw.io et place :  
1) API/Serveur → Base OLTP  
2) Debezium (CDC) → Airflow  
3) Deux flèches qui partent de Airflow :  
   a) vers Snowflake (quasi temps-réel)  
   b) vers un service de fraude + MongoDB  
4) Batch Airflow → Snowflake pour l’historique lourd  
Entoure chaque bloc d’une couleur : bleu = OLTP, vert = OLAP, orange = NoSQL.  
Ajoute des cadenas sur les traits pour signifier « TLS chiffré ».

Étape 3 – Modèle OLTP 
Sur dbdiagram.io tape :

```
Table merchants {
  id uuid [pk]
  name text
  created_at timestamptz
}

Table customers {
  id uuid [pk]
  email text
  created_at timestamptz
}

Table transactions {
  id uuid [pk]
  merchant_id uuid [ref: > merchants.id]
  customer_id uuid [ref: > customers.id]
  amount numeric(14,2)
  currency char(3)
  status text  -- success / failed / refunded
  created_at timestamptz
}
```

(ajoute chargebacks, refunds si tu as le temps). Ce diagramme = ERD.

Étape 4 – Modèle OLAP
Table FACT_TRANSACTIONS (clé surrogate + fk vers dimens).  
Dimensions : dim_date, dim_merchant, dim_customer, dim_payment_method, dim_geo.  
Dans ton doc, écris : « SCD Type 2 pour les marchands ».  
Explique : « partitions par date, clustering par merchant_id ».  
Ajoute deux vues matérialisées : revenue_daily, churn_weekly.

Étape 5 – Modèle NoSQL 
Dans le doc dis : « Collection session_events » avec ce JSON :

```
{
  "session_id": "...",
  "user_id": "...",
  "device": "mobile",
  "events": [
    {"ts":"2024-06-11T10:00:00Z","type":"click","target":"pay_button"},
    ...
  ],
  "ip":"1.2.3.4",
  "created_at":"..."
}
```

Index {user_id, created_at}. TTL 30 jours.  
Autre collection fraud_features {txn_id, feature_vector:{...}, label}.  

Étape 6 – Pipeline 
Écris 10 lignes dans ton dossier :  
• Debezium lit les WAL Postgres et pousse sur Airflow topics txn_created, txn_updated.  
• bigquery consomme ces topics via Airflow Connect → table raw_txn.  
• Airflow job nightly fait : raw_txn → facts/dimensions via dbt.  
• Airflow Streams calcule en temps réel les features de fraude et écrit dans Mongo + expose score via gRPC.  
• S3 est le data lake où Airflow stocke les parquets d’archive.

Étape 7 – Sécurité / conformité 
Bullets :  
• TLS 1.2 partout, chiffrement at-rest AES-256 (KMS).  
• Tokenisation de tout ce qui ressemble à un numéro de carte (PCI-DSS).  
• RBAC : Snowflake RLS, Postgres row-level security, Mongo SCRAM + VPC.  
• Audit logs → SIEM.  
• GDPR : API “delete user” qui déclenche un DAG Airflow effaçant/anon les données dans les trois stores.

Étape 8 – ML
Bullets :  
• Feature store = Mongo (temps réel) + table snowflake (batch).  
• Modèle XGBoost enregistré dans MLflow, déployé sur SageMaker.  
• Endpoint consulté synchrone lors de chaque tentative de paiement via Airflow RPC.  
• Monitoring : Prometheus + concept drift check nightly.

Étape 9 – Exemples de requêtes 

bigquery : revenu mensuel  
```
SELECT merchant_id,
       DATE_TRUNC('month', txn_date) AS month,
       SUM(amount) AS revenue
FROM FACT_TRANSACTIONS
WHERE status='success'
GROUP BY 1,2
ORDER BY 3 DESC;
```

Mongo (Node.js style) – sessions trop rapides  
```
db.session_events.aggregate([
  {$unwind:"$events"},
  {$group:{_id:"$session_id", evt_per_min:{$avg:{$divide:[1,{$subtract:["$events.ts", "$created_at"]}/60000]}}}},
  {$match:{evt_per_min:{$gt:20}}}
])
```

Étape 10 – Mise en forme et rendu
• Exporte les dessins en PNG et glisse-les dans un Google Doc ou un PowerPoint.  
• Ajoute titre + table "Critère 1.1 ⇄ Section A", etc.  
• Vérifie l’orthographe, mets ton nom, sauve en PDF.  

────────────────────────
III. CE QUI COMPTE LE PLUS  
1) Montrer que je sais relier trois mondes (OLTP ↔ Airflow ↔ OLAP/NoSQL).  
2) Sécurité / conformité (chiffrement, PCI, RGPD).  
3) Scalabilité (partition, réplication, streaming).  
Ne perds pas 1 h sur un beau dessin ; un schéma clair suffit.  

────────────────────────
