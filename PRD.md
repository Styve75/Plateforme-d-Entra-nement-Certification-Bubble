
# Spécifications Fonctionnelles : Plateforme d'Entraînement Certification Bubble

## 1. Vision du Produit
Une plateforme d'élite destinée aux développeurs Bubble pour préparer les certifications **Associate** et **Developer**. L'objectif est d'atteindre un niveau de maîtrise de **97,3 %** (85/90 bonnes réponses) via un parcours d'apprentissage forcé et une validation rigoureuse.

---

## 2. Roadmap de Développement

### PHASE 1 : MVP (Le Cœur & Le Parcours Forcé)
* **Sélecteur de Certification :** Choix entre le parcours *Associate* ou *Developer* dès l'inscription.
* **Système d'Entraînement par Thème :** * Thèmes : Database, Workflows, API, Privacy, Logic, UI/Responsive.
    * Mode "Custom Training" : Création de sessions sur mesure par l'utilisateur.
* **Verrouillage de l'Examen (Condition de succès) :**
    * L'examen final de 90 questions est verrouillé par défaut.
    * **Condition :** Avoir complété 100% des séries d'entraînement dans chaque thème.
* **Moteur d'Examen :**
    * Mode Entraînement (correction immédiate).
    * Mode Examen Blanc (90 questions, chrono, score final).
* **Seuil d'Excellence :** Calcul strict du taux de réussite (Objectif : 97,3%).

### PHASE 2 : V1 (Compétition & Crédibilité)
* **Leaderboard Global :** Classement public basé sur l'Accuracy Rate global et le meilleur temps à l'examen.
* **Feedback Pédagogique :** Liens vers la documentation officielle pour chaque erreur.
* **Scénarios de Débugging :** Questions basées sur des audits visuels (captures d'écran complexes).
* **Analyse de Performance :** Graphique radar (Spider Chart) montrant les forces/faiblesses par catégorie.
* **Badge "Certified Ready" :** Génération d'un certificat de réussite interne.

### PHASE 3 : V2 (Automatisation IA & IA Mentor)
* **Maintenance IA :** Mise à jour quotidienne des questions par l'IA en fonction des *Release Notes* de Bubble.
* **IA Mentor :** Analyse des erreurs récurrentes et suggestions de parcours personnalisés pour combler les lacunes.
* **Sandbox Interactive :** Interface simplifiée pour tester visuellement les logiques de workflows ou Privacy Rules.

### HORS-PÉRIMÈTRE (Évolutions Futures)
* Ghost Mode (barre de progression comparative en temps réel).
* Espace Communautaire (forums par question).
* Job Board & Mise en relation recruteurs.

---

## 3. Architecture des Données (Data Schema)

| Type de Donnée | Champs Principaux | Description |
| :--- | :--- | :--- |
| **User** | Email, Certification_Type, Global_Accuracy, Best_Time | Profil utilisateur et stats globales. |
| **Question** | Text, Image, Category (API, Data...), Certification (Assoc/Dev), Correct_Answer, Explanation_Link | La base de connaissance. |
| **Theme_Progress** | User (Link), Category, Completed_Questions (Count), Total_Questions (Count) | Suivi pour le déblocage de l'examen. |
| **Exam_Attempt** | User (Link), Date, Score, Time_Taken, Is_Final_Exam (Boolean) | Historique des sessions de tests. |
| **Leaderboard** | User (Link), Rank, Score_Max, Time_Min | Vue agrégée pour la page d'accueil. |

---

## 4. Logique de Calcul (Business Rules)

* **Calcul du Score :** $Score \% = (\frac{\text{Réponses Correctes}}{\text{Total Questions}}) \times 100$
* **Validation Examen :** Si $Score \% \ge 97,3$, alors le statut passe à `Certified Ready`.
* **Mise à jour IA :** L'agent IA doit vérifier chaque jour le champ `Update_Date` des questions et les comparer aux dernières nouveautés de l'éditeur Bubble.

---

## 5. Interface Utilisateur (UI) - Éléments Clés

1.  **Dashboard :** Liste des thèmes avec barres de progression circulaires. Bouton "Passer l'Examen" grisé tant que les barres ne sont pas à 100%.
2.  **Interface Examen :** Épurée, focus sur la question, barre de progression linéaire en haut, bouton "Review Later".
3.  **Page Leaderboard :** Tableau triable par Taux de réussite et Temps, mettant en avant le Top 3 (Podium).

---

> **Note pour l'Agent IA de codage :** Prioriser la mise en place du système de verrouillage (Logic de déblocage de l'examen final basée sur la complétion des thèmes) dès le début du développement de la base de données.