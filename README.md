# 💰 Expense Tracker Backend - Spring Boot 3 & Java 21

Ce projet est le backend professionnel d'une application de gestion des dépenses personnelles (**Expense Tracker**). Conçu en suivant les bonnes pratiques **SOLID** et une architecture en couches propre, il est hautement performant, sécurisé et prêt pour la production.

---

## 🚀 Fonctionnalités Clés

### 🔒 Sécurité et Authentification
*   **Système d'inscription et de connexion** avec validation d'emails uniques.
*   **Authentification sans état (Stateless) par JWT** (JSON Web Tokens).
*   **Hachage des mots de passe avec BCrypt** (Spring Security).
*   Gestion globale des accès : toutes les routes sont sécurisées sauf l'authentification et Swagger UI.

### 📁 Gestion des Catégories (Isolées par Utilisateur)
*   **CRUD Complet** : Création, modification, lecture, et suppression.
*   Chaque utilisateur gère ses propres catégories personnalisées avec des attributs de style (`name`, `color`, `icon`).
*   Sécurité anti-suppression accidentelle si la catégorie possède des dépenses rattachées.

### 💸 Gestion des Dépenses (Expenses)
*   **CRUD Complet** pour consigner chaque dépense (`title`, `description`, `amount`, `expenseDate`, etc.).
*   **Filtres de recherche avancés** et combinatoires :
    *   Filtrage par Catégorie.
    *   Filtrage par Période (Date de début et Date de fin).
    *   Filtrage par montant minimum et maximum.

### 📊 Budgets Mensuels & Notifications
*   Définition d'un budget maximum par mois et par année (`month`, `year`, `amount`).
*   **Calculs financiers automatiques** :
    *   Montant cumulé dépensé sur le mois.
    *   Solde restant.
    *   Pourcentage de consommation du budget.
*   **Système de Notification d'alerte** intégré : si le cumul des dépenses mensuelles dépasse le budget défini, l'API renvoie un avertissement actif (`warning=true`, `message="Budget dépassé"`).

### 📈 Dashboard & Statistiques Avancées
*   Endpoints consolidés en une seule requête optimisée en mémoire :
    *   Total général des dépenses et nombre de transactions.
    *   Total consommé pour le mois en cours.
    *   Répartition complète des dépenses par catégorie.
    *   Top 5 des catégories de dépenses avec pourcentages.
    *   Évolution mensuelle historique (tendance des 6 derniers mois).

---

## 🛠️ Stack Technique

*   **Runtime** : Java 21 (LTS)
*   **Framework** : Spring Boot 3.3.1
*   **Sécurité** : Spring Security & JJWT (Json Web Token)
*   **Persistance** : Spring Data JPA, Hibernate, PostgreSQL
*   **Compilation** : Maven 3
*   **Utilitaires** : Lombok, MapStruct (DTO Mapping), Jakarta Validation
*   **Documentation** : Springdoc Swagger UI (OpenAPI 3)
*   **DevOps** : Docker, Docker-Compose

---

## 📁 Structure du Projet

L'architecture respecte scrupuleusement l'organisation en couches :

```text
com.expensetracker
│
├── config                      # Configuration globale (Swagger OpenAPI)
├── controller                  # Contrôleurs REST (Exposition des Endpoints)
├── dto                         # Objets de Transfert de Données (DTO)
│     ├── request               # Formulaires reçus (Validation @NotBlank, etc.)
│     └── response              # Réponses standardisées retournées
├── entity                      # Entités JPA d'accès à la base PostgreSQL
├── repository                  # Interfaces Spring Data JPA
├── security                    # Composants Spring Security
│      ├── jwt                  # Filtres, Utilities et Point d'entrée JWT
│      ├── config               # Chaîne de filtres de sécurité (WebSecurityConfig)
│      └── service              # Service utilisateur (UserDetails / UserDetailsService)
├── service                     # Logique Métier
│      ├── impl                 # Implémentations concrètes des services
│      └── interfaces           # Contrats d'interfaces de services
├── exception                   # Gestionnaires et exceptions personnalisées
├── mapper                      # Interfaces de mapping MapStruct
├── utils                       # Utilitaires système (SecurityUtils)
└── ExpenseTrackerApplication   # Point d'entrée principal de l'application
```

---

## 🐳 Comment Lancer le Projet ?

### Prérequis
*   **Docker** & **Docker-Compose** installés sur votre machine OU **Java 21** et **PostgreSQL**.

### Méthode 1 : Démarrage Rapide avec Docker Compose (Recommandé)
Démarre automatiquement l'application Spring Boot et une base de données PostgreSQL isolée.

1.  Placez-vous dans le dossier `/backend`.
2.  Exécutez la commande suivante :
    ```bash
    docker-compose up --build
    ```
3.  Le serveur démarrera sur le port **8080**.

### Méthode 2 : Lancement Local avec Maven
Si vous préférez exécuter l'application sur votre système :

1.  Assurez-vous qu'une instance PostgreSQL tourne localement sur le port `5432` avec une base nommée `expensetracker`.
2.  Configurez vos variables d'environnement ou laissez les valeurs par défaut définies dans `/src/main/resources/application.yml`.
3.  Compilez et lancez l'application :
    ```bash
    mvn clean spring-boot:run
    ```

---

## 📚 Documentation API & Swagger

Une fois l'application lancée, la documentation Swagger interactive est accessible aux adresses suivantes :

*   **Interface Graphique Swagger UI** : `http://localhost:8080/api/swagger-ui.html`
*   **Spécification JSON OpenAPI** : `http://localhost:8080/api/v3/api-docs`

> 💡 **Tip d'authentification dans Swagger UI** :
> 1. Appelez le endpoint `/api/auth/login` pour récupérer le token de session.
> 2. Cliquez sur le bouton **Authorize** en haut à droite de l'interface Swagger.
> 3. Collez votre token JWT (sans le préfixe Bearer, juste la chaîne de caractères) et validez !

---

## 📌 Liste complète des Endpoints REST

Toutes les requêtes de données retournent une structure JSON standardisée :
```json
{
    "success": true,
    "message": "...",
    "data": { ... }
}
```

### 🔐 Authentification (Public)
*   `POST /api/auth/register` : Crée un compte utilisateur.
*   `POST /api/auth/login` : Connecte un utilisateur et retourne un JWT Token.

### 📁 Gestion des Catégories (Privé - JWT Requis)
*   `GET /api/categories` : Liste toutes les catégories de l'utilisateur.
*   `POST /api/categories` : Crée une nouvelle catégorie.
*   `PUT /api/categories/{id}` : Modifie une catégorie existante.
*   `DELETE /api/categories/{id}` : Supprime une catégorie (si non rattachée à des dépenses).

### 💸 Gestion des Dépenses (Privé - JWT Requis)
*   `GET /api/expenses` : Liste toutes les dépenses de l'utilisateur.
*   `GET /api/expenses/{id}` : Récupère les détails d'une dépense spécifique.
*   `POST /api/expenses` : Enregistre une nouvelle dépense.
*   `PUT /api/expenses/{id}` : Modifie une dépense existante.
*   `DELETE /api/expenses/{id}` : Supprime une dépense.
*   `GET /api/expenses/filter` : Filtre les dépenses selon les critères de recherche.

### 💰 Gestion des Budgets (Privé - JWT Requis)
*   `GET /api/budget` : Récupère l'état du budget mensuel (total dépenses, montant restant, pourcentage, alertes).
*   `POST /api/budget` : Configure un nouveau budget mensuel.
*   `PUT /api/budget` : Modifie le budget du mois en cours.

### 📊 Tableau de Bord (Privé - JWT Requis)
*   `GET /api/dashboard` : Synthèse globale rapide.
*   `GET /api/dashboard/statistics` : Métriques détaillées, répartition par catégories et historique mensuel.

# 💰 Expense Tracker Frontend — React

Ce projet est l'interface utilisateur (frontend) de l'application **Expense Tracker**, développée en **React**. Elle consomme l'API REST du [backend Spring Boot](../backend) pour offrir une expérience complète de gestion des dépenses personnelles : authentification, catégories, dépenses, budgets mensuels et tableau de bord statistique.

---

## 🚀 Fonctionnalités clés

### 🔒 Authentification
* Pages d'inscription et de connexion.
* Stockage sécurisé du token **JWT** côté client (mémoire / stockage sécurisé).
* Routes protégées : redirection automatique vers la page de connexion si le token est absent ou expiré.
* Déconnexion avec invalidation locale de la session.

### 📁 Gestion des catégories
* Liste des catégories avec icônes et couleurs personnalisées.
* Création, modification et suppression de catégories.
* Message d'erreur clair si une suppression est bloquée (catégorie liée à des dépenses).

### 💸 Gestion des dépenses
* Liste des dépenses avec pagination.
* Formulaire d'ajout / modification (`title`, `description`, `amount`, `expenseDate`, catégorie).
* Filtres combinables : par catégorie, par période, par montant min/max.
* Suppression avec confirmation.

### 📊 Budgets mensuels
* Configuration du budget par mois / année.
* Affichage du montant dépensé, du solde restant et du pourcentage de consommation (barre de progression).
* Bannière d'alerte visuelle si le budget est dépassé.

### 📈 Dashboard & statistiques
* Vue de synthèse : total des dépenses, nombre de transactions, total du mois en cours.
* Graphique de répartition des dépenses par catégorie.
* Top 5 des catégories les plus dépensières.
* Graphique d'évolution mensuelle (tendance des 6 derniers mois).

---

## 🛠️ Stack technique

| Domaine | Technologie |
|---|---|
| Librairie UI | React 18 + TypeScript |
| Build tool | Vite |
| Routing | React Router (routes centralisées dans `AppRoutes.tsx`) |
| Requêtes HTTP | Services dédiés (dossier `services/`) |
| Gestion d'état | Context API |
| Typage | TypeScript (`types.ts`) |
| Style | CSS (`index.css`) |
| Gestionnaire de paquets | npm |

---

## 📁 Structure du projet

```text
frontend/
│
├── assets/                      # Images, icônes, ressources statiques
├── node_modules/
├── src/
│   ├── components/              # Composants réutilisables (boutons, cartes, formulaires, etc.)
│   ├── context/                 # Contextes React (authentification, thème, etc.)
│   ├── layout/                  # Structure générale de l'application
│   │   ├── MainLayout.tsx       # Layout principal englobant Navbar + Sidebar + contenu
│   │   ├── Navbar.tsx           # Barre de navigation supérieure
│   │   └── Sidebar.tsx          # Menu latéral de navigation
│   ├── pages/                   # Pages principales de l'application
│   │   ├── Connexion.tsx        # Page de connexion
│   │   ├── Inscription.tsx      # Page d'inscription
│   │   ├── TableauDeBord.tsx    # Dashboard / vue de synthèse
│   │   ├── Categories.tsx       # Gestion des catégories
│   │   ├── Depenses.tsx         # Gestion des dépenses
│   │   ├── Budget.tsx           # Configuration et suivi du budget mensuel
│   │   ├── Statistiques.tsx     # Statistiques détaillées
│   │   └── Profil.tsx           # Profil utilisateur
│   ├── routes/
│   │   └── AppRoutes.tsx        # Définition centralisée des routes (publiques / protégées)
│   ├── services/                # Appels vers l'API backend (auth, categories, expenses, budget...)
│   ├── App.tsx                  # Composant racine
│   ├── main.tsx                 # Point d'entrée de l'application
│   ├── index.css                # Styles globaux
│   └── types.ts                 # Types et interfaces TypeScript partagés
├── .env.example                 # Exemple de variables d'environnement
├── .gitignore
├── index.html                   # Point d'entrée HTML (Vite)
├── metadata.json
├── package.json
└── package-lock.json
```

---

## ⚙️ Configuration

Créez un fichier `.env` à la racine du projet en vous basant sur `.env.example` :

```env
VITE_API_BASE_URL=http://localhost:8080/api
```

> L'URL doit correspondre à celle du backend Spring Boot (voir le README du backend).

---

## 🐳 Comment lancer le projet ?

### Prérequis
* **Node.js** (version 18 ou supérieure)
* **npm** ou **yarn**
* Le [backend](../backend) doit être démarré et accessible (par défaut sur le port `8080`)

### Installation et démarrage

1. Installez les dépendances :
   ```bash
   npm install
   ```
2. Configurez le fichier `.env` (voir section **Configuration**).
3. Lancez le serveur de développement :
   ```bash
   npm run dev
   ```
4. L'application sera accessible sur : `http://localhost:5173`

### Build de production

```bash
npm run build
```

Les fichiers optimisés seront générés dans le dossier `dist/`.

---

## 🔑 Authentification côté client

1. L'utilisateur se connecte via la page **Connexion**, qui appelle `/api/auth/login` par l'intermédiaire du service d'authentification (`services/`).
2. Le token JWT reçu est stocké côté client et ajouté automatiquement dans l'en-tête `Authorization: Bearer <token>` de chaque requête vers le backend.
3. Le contexte d'authentification (`context/`) gère l'état de session et protège les routes privées définies dans `AppRoutes.tsx` ; toute réponse `401 Unauthorized` redirige l'utilisateur vers la page **Connexion**.

---

## 📌 Pages principales

| Page | Fichier | Description | Accès |
|---|---|---|---|
| Connexion | `pages/Connexion.tsx` | Connexion utilisateur | Public |
| Inscription | `pages/Inscription.tsx` | Création de compte | Public |
| Tableau de bord | `pages/TableauDeBord.tsx` | Vue de synthèse et statistiques rapides | Privé |
| Catégories | `pages/Categories.tsx` | Gestion des catégories | Privé |
| Dépenses | `pages/Depenses.tsx` | Liste, création et filtres des dépenses | Privé |
| Budget | `pages/Budget.tsx` | Configuration et suivi du budget mensuel | Privé |
| Statistiques | `pages/Statistiques.tsx` | Statistiques détaillées et graphiques | Privé |
| Profil | `pages/Profil.tsx` | Informations et paramètres du compte utilisateur | Privé |

La navigation entre les pages privées se fait via le layout commun `MainLayout.tsx`, qui inclut la `Navbar` (en-tête) et la `Sidebar` (menu latéral).
