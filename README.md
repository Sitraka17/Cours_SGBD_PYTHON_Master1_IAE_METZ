# Cours_SGBD_PYTHON_Master1_IAE_METZ
Ceci est le repository pour les étudiants IAE METZ 2027 
# 🎓 Projet Final : Analyse et Modélisation de Données Financières

Ce document définit les attendus et les jalons pour la validation du projet final. L'objectif est de concevoir une solution analytique ou applicative complète, depuis l'extraction des données jusqu'à la restitution, autour d'une problématique financière concrète.

---

## 1. 👥 Formation du Groupe
La première étape consiste à constituer votre équipe de travail et à définir l'organisation interne.
* **Composition :** [ 1 à 4 membres].
* **Organisation technique :** Définissez clairement qui gère quelles parties de la stack (ex: Data Ingestion, Modélisation, Développement Backend/Frontend, Gestion du repository).
* **Action requise :** Valider la composition du groupe et le nom de l'équipe avant le **11 AVRIL 2026**.

## 2. 🎯 Choix du Sujet Financier
Le sujet doit répondre à une problématique métier précise. Voici quelques axes techniques d'exploration :
* **Trading algorithmique / Prédiction :** Modélisation de séries temporelles (ARIMA, LSTMs) pour anticiper les tendances de marché.
* **Analyse de risques :** Création d'un modèle de *Credit Scoring* ou calcul de la *Value at Risk* (VaR) sur un portefeuille d'actifs.
* **Analyse de sentiment :** Utilisation de modèles NLP (Natural Language Processing) pour évaluer l'impact de l'actualité économique sur les cours boursiers.
* **Action requise :** Rédiger un court *Problem Statement* (problème à résoudre, approche envisagée) pour validation par l'équipe pédagogique.

## 3. ⚙️ Pipeline de Données et Stack Technique
Votre projet doit démontrer une véritable ingénierie autour de la donnée. L'architecture technique doit être lisible et justifiable :
* **Acquisition (Sourcing) :** Intégration d'APIs financières REST/WebSocket (ex: *Alpha Vantage, Yahoo Finance, Bloomberg*), web scraping, ou utilisation de bases de données publiques (Kaggle, Banque Mondiale).
* **Traitement (ETL) :** Nettoyage, transformation et structuration des données via des bibliothèques adaptées (`pandas`, `NumPy`, ou `PySpark` pour de gros volumes).
* **Stockage :** Sauvegarde des données propres dans une base relationnelle (`PostgreSQL`, `SQLite`) ou NoSQL (`MongoDB`).
* **Analyse :** Implémentation des algorithmes ou des modèles statistiques pertinents pour votre problématique.

## 4. 🚀 Livrables et Présentation
L'évaluation finale portera sur la robustesse du code, la pertinence de l'analyse et la clarté de la restitution.
* **Le Repository :**
  * Un code source propre, modulaire et versionné (Git).
  * Un fichier `requirements.txt` ou `Pipfile` listant l'environnement et les dépendances.
  * Un `README.md` propre au projet détaillant l'architecture, l'installation locale (`setup`) et les instructions d'exécution.
* **Le Support de Présentation :** Des slides synthétisant le contexte métier, l'architecture technique choisie, les résultats obtenus et les limites de votre modèle.
* **La Démonstration (Live Demo) :** Présentation interactive via un dashboard (ex: `Streamlit`, `Dash`) ou l'exécution d'un notebook Jupyter documenté illustrant vos résultats finaux.
