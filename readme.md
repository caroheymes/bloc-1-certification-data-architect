# Spotify Data Governance & Architecture — Certification RNCP 38777 (Bloc 1)

**Projet de certification pour le titre d'Architecte en Intelligence Artificielle (RNCP 38777 - Bloc de
compétences 1).**
Ce dépôt rassemble les livrables d'évaluation de la maturité, de la politique de gouvernance et du plan
d'implémentation opérationnel des données appliqués au cas d'usage de **Spotify Technology S.A.**

---


![Target](https://img.shields.io/badge/Target-Spotify-1DB954?style=for-the-badge&logo=spotify&logoColor=white)
![Framework](https://img.shields.io/badge/Framework-DAMA_DMBOK2-0052CC?style=for-the-badge)
![Security](https://img.shields.io/badge/Security-ISO%2FIEC_27001%3A2022-E11D48?style=for-the-badge&logo=roots)
![Compliance](https://img.shields.io/badge/Compliance-GDPR%20%2F%20RGPD-FF5722?style=for-the-badge&logo=gdpr&logoColor=white)
![Accessibility](https://img.shields.io/badge/Accessibility-RGAA%20%2F%20WCAG%202.2-8B5CF6?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)



## Présentation du Projet

L'objectif de ce projet est de concevoir et de déployer un **cadre robuste de gouvernance des données** pour
Spotify, structuré autour des meilleures pratiques du marché (**DAMA-DMBOK2**) et assurant une conformité stricte
avec les exigences de sécurité (**ISO/IEC 27001:2022**), réglementaires (**RGPD**) et d'accessibilité numérique
(**RGAA 4.1.2 / WCAG 2.2**).

Le projet s'articule autour d'une transition de maturité des données (visant le **niveau 4 de Gartner**) et se
concrétise par un **projet pilote de 6 mois** focalisé sur la **division marketing** (optimisation du ciblage
publicitaire et gestion des consentements).


## Organisation des Livrables

Le dépôt est structuré comme suit :

### [Livrable 1] Évaluation de la Maturité des Données (Maturity Assessment)
* Évaluation sémantique et clinique détaillée des 9 dimensions clés de la gouvernance basées sur le référentiel
**DAMA**.
* Analyse de l'état actuel (*As-Is*) et définition de la cible stratégique (*To-Be*).
* *Formats disponibles :* `.md` (Markdown original) / `.pdf`.

### [Livrable 2] Politique de Gouvernance & Charte de Données
* Définition de la charte de gouvernance des données, des principes fondamentaux et de l'organisation humaine du
**Centre d'Excellence (CoE)**.
* Mise en place des rôles opérationnels (CDO, DPO, Data Stewards, Data Owner, Data Custodians).
* Spécifications sur le lignage des données (*data lineage*), la sécurité périmétrique et l'accessibilité des
outils de Business Intelligence.
* *Formats disponibles :* `.md` / `.html` (Version interactive double-thème clair/sombre) / `.pdf`.

### [Livrable 3] Plan d'Implémentation Globale & Plan Pilote
* Feuille de route opérationnelle sur **18 mois** divisée en 3 phases (Fondation, Industrialisation,
Optimisation).
* Détail du **projet pilote marketing de 6 mois** (sources, ingestion, catalogage via Collibra, et consentement
via OneTrust).
* Matrice **RACI** étendue des instances de décision et d'exécution.
* **Protocole de gestion de crise** pas-à-pas en cas d'incident de données majeur (chronologie d'urgence T0 à T+
48h).
* *Formats disponibles :* `.md` / `.html` (Version interactive avec timeline vectorielle adaptative et diagrammes
de crise) / `.pdf`.

### [Livrable 4] Grille d'Évaluation de Conformité
* Matrice d'audit de conformité technique et réglementaire.
* *Formats disponibles :* `.xlsx` (Tableau Excel structuré).

###  [Livrable 5] Soutenance de Certification
* Support visuel synthétisant l'ensemble de la démarche d'architecture et de gouvernance pour la présentation
orale devant le jury.
* *Formats disponibles :* `.pptx` (Version Microsoft PowerPoint).

  ---

## Stack Technique & Outils du Cadre Cible

Le projet intègre et documente l'architecture des solutions de gouvernance d'entreprise suivantes :
* **Gouvernance & Métadonnées** : [Collibra Data Intelligence Cloud](https://www.collibra.com/)
* **Gestion de la Vie Privée & Consentement** : [OneTrust](https://www.onetrust.com/)
* **Catalogue Technique & Métriques** : [Backstage (by Spotify)](https://backstage.spotify.com/) via le contrôle
des fichiers `owner.yaml`
* **Qualité des Données & Ingestion** : Talend / Apache Spark / BigQuery
* **Visualisation** : Tableau / Looker (Mise en conformité d'accessibilité RGAA/WCAG)

  ---

## Particularités techniques de ce Dépôt

* **Feuille de Route Vectorielle Responsive** : Remplacement des diagrammes Gantt textuels standards par une
frise chronologique vectorielle native SVG interactive. Elle est optimisée pour s'afficher harmonieusement en
impression de qualité (fond clair) et s'adapter au thème d'écran (Mode sombre Spotify).
* **Robustesse des Diagrammes Mermaid** : L'ensemble des diagrammes (flowcharts techniques, diagrammes de
séquence de crise et matrice des risques) est optimisé pour les versions récentes du moteur de rendu Mermaid (v11.
15.0+), garantissant l'absence d'erreurs de syntaxe sur GitHub et lors de la compilation.
* **Mise en page CSS Paged Media** : Les livrables HTML intègrent des directives d'impression CSS haut de gamme
pour forcer des sauts de page A4 nets, évitant les coupures de tableaux ou de paragraphes à cheval sur deux pages.

  ---

## Auteur

* **Caroline HEYMES** — *Candidate à la Certification Architecte en IA (RNCP 38777)*
──────
Note: Ce projet s'inscrit dans un cadre d'études académiques et de certification professionnelle, basé sur les
informations publiques de Spotify S.A.
