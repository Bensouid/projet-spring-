# 💰 Expense Tracker Backend — Spring Boot 3 & Java 21

Ce projet est le backend professionnel d'une application de gestion des dépenses personnelles (**Expense Tracker**). Conçu en suivant les bonnes pratiques **SOLID** et une architecture en couches propre, il est performant, sécurisé et prêt pour la production.

---

## 🚀 Fonctionnalités clés

### 🔒 Sécurité et authentification
* Système d'inscription et de connexion avec validation d'e-mails uniques.
* Authentification sans état (**stateless**) par **JWT** (JSON Web Token).
* Hachage des mots de passe avec **BCrypt** (Spring Security).
* Gestion globale des accès : toutes les routes sont sécurisées, à l'exception de l'authentification et de Swagger UI.

### 📁 Gestion des catégories (isolées par utilisateur)
* CRUD complet : création, modification, lecture et suppression.
* Chaque utilisateur gère ses propres catégories personnalisées avec des attributs de style (`name`, `color`, `icon`).
* Protection contre la suppression accidentelle : une catégorie liée à des dépenses ne peut pas être supprimée.

### 💸 Gestion des dépenses (Expenses)
* CRUD complet pour consigner chaque dépense (`title`, `description`, `amount`, `expenseDate`, etc.).
* Filtres de recherche avancés et combinables :
  * par catégorie ;
  * par période (date de début et date de fin) ;
  * par montant minimum et maximum.

### 📊 Budgets mensuels & notifications
* Définition d'un budget maximum par mois et par année (`month`, `year`, `amount`).
* Calculs financiers automatiques :
  * montant cumulé dépensé sur le mois ;
  * solde restant ;
  * pourcentage de consommation du budget.
* Système d'alerte intégré : si le cumul des dépenses mensuelles dépasse le budget défini, l'API renvoie un avertissement actif (`warning: true`, `message: "Budget dépassé"`).

### 📈 Dashboard & statistiques avancées
Endpoints consolidés en une seule requête optimisée en mémoire :
* total général des dépenses et nombre de transactions ;
* total consommé pour le mois en cours ;
* répartition complète des dépenses par catégorie ;
* top 5 des catégories de dépenses avec pourcentages ;
* évolution mensuelle historique (tendance des 6 derniers mois).

---

## 🛠️ Stack technique

| Domaine | Technologie |
|---|---|
| Runtime | Java 21 (LTS) |
| Framework | Spring Boot 3.3.1 |
| Sécurité | Spring Security & JJWT (JSON Web Token) |
| Persistance | Spring Data JPA, Hibernate, PostgreSQL |
| Compilation | Maven 3 |
| Utilitaires | Lombok, MapStruct (mapping DTO), Jakarta Validation |
| Documentation | Springdoc Swagger UI (OpenAPI 3) |
| DevOps | Docker, Docker Compose |

---

## 📁 Structure du projet

L'architecture respecte une organisation en couches :

```text
com.expensetracker
│
├── config                      # Configuration globale (Swagger OpenAPI)
├── controller                  # Contrôleurs REST (exposition des endpoints)
├── dto                         # Objets de transfert de données (DTO)
│   ├── request                 # Formulaires reçus (validation @NotBlank, etc.)
│   └── response                # Réponses standardisées retournées
├── entity                      # Entités JPA d'accès à la base PostgreSQL
├── repository                  # Interfaces Spring Data JPA
├── security                    # Composants Spring Security
│   ├── jwt                     # Filtres, utilitaires et point d'entrée JWT
│   ├── config                  # Chaîne de filtres de sécurité (WebSecurityConfig)
│   └── service                 # Service utilisateur (UserDetails / UserDetailsService)
├── service                     # Logique métier
│   ├── impl                    # Implémentations concrètes des services
│   └── interfaces              # Contrats d'interfaces de services
├── exception                   # Gestionnaires et exceptions personnalisées
├── mapper                      # Interfaces de mapping MapStruct
├── utils                       # Utilitaires système (SecurityUtils)
└── ExpenseTrackerApplication   # Point d'entrée principal de l'application
```

---

## 🐳 Comment lancer le projet ?

### Prérequis
* **Docker** et **Docker Compose** installés, **ou**
* **Java 21** et **PostgreSQL** installés localement.

### Méthode 1 : démarrage rapide avec Docker Compose (recommandé)
Cette méthode démarre automatiquement l'application Spring Boot et une base de données PostgreSQL isolée.

1. Placez-vous dans le dossier `/backend`.
2. Exécutez la commande suivante :
   ```bash
   docker-compose up --build
   ```
3. Le serveur démarre sur le port **8080**.

### Méthode 2 : lancement local avec Maven
Si vous préférez exécuter l'application directement sur votre système :

1. Assurez-vous qu'une instance PostgreSQL tourne localement sur le port `5432`, avec une base nommée `expensetracker`.
2. Configurez vos variables d'environnement, ou laissez les valeurs par défaut définies dans `/src/main/resources/application.yml`.
3. Compilez et lancez l'application :
   ```bash
   mvn clean spring-boot:run
   ```

---

## 📚 Documentation API & Swagger

Une fois l'application lancée, la documentation Swagger interactive est accessible aux adresses suivantes :

* **Interface graphique Swagger UI** : `http://localhost:8080/api/swagger-ui.html`
* **Spécification JSON OpenAPI** : `http://localhost:8080/api/v3/api-docs`

> 💡 **Astuce — s'authentifier dans Swagger UI**
> 1. Appelez l'endpoint `/api/auth/login` pour récupérer votre token JWT.
> 2. Cliquez sur le bouton **Authorize** en haut à droite de l'interface Swagger.
> 3. Collez votre token JWT (sans le préfixe `Bearer `, juste la chaîne de caractères) et validez.

---

## 📌 Liste complète des endpoints REST

Toutes les réponses de l'API suivent une structure JSON standardisée :
```json
{
  "success": true,
  "message": "...",
  "data": { ... }
}
```

### 🔐 Authentification (public)
| Méthode | Endpoint | Description |
|---|---|---|
| POST | `/api/auth/register` | Crée un compte utilisateur |
| POST | `/api/auth/login` | Connecte un utilisateur et retourne un token JWT |

### 📁 Gestion des catégories (privé — JWT requis)
| Méthode | Endpoint | Description |
|---|---|---|
| GET | `/api/categories` | Liste toutes les catégories de l'utilisateur |
| POST | `/api/categories` | Crée une nouvelle catégorie |
| PUT | `/api/categories/{id}` | Modifie une catégorie existante |
| DELETE | `/api/categories/{id}` | Supprime une catégorie (si aucune dépense n'y est rattachée) |

### 💸 Gestion des dépenses (privé — JWT requis)
| Méthode | Endpoint | Description |
|---|---|---|
| GET | `/api/expenses` | Liste toutes les dépenses de l'utilisateur |
| GET | `/api/expenses/{id}` | Récupère les détails d'une dépense spécifique |
| POST | `/api/expenses` | Enregistre une nouvelle dépense |
| PUT | `/api/expenses/{id}` | Modifie une dépense existante |
| DELETE | `/api/expenses/{id}` | Supprime une dépense |
| GET | `/api/expenses/filter` | Filtre les dépenses selon les critères de recherche |

### 💰 Gestion des budgets (privé — JWT requis)
| Méthode | Endpoint | Description |
|---|---|---|
| GET | `/api/budget` | Récupère l'état du budget mensuel (total dépensé, montant restant, pourcentage, alertes) |
| POST | `/api/budget` | Configure un nouveau budget mensuel |
| PUT | `/api/budget` | Modifie le budget du mois en cours |

### 📊 Tableau de bord (privé — JWT requis)
| Méthode | Endpoint | Description |
|---|---|---|
| GET | `/api/dashboard` | Synthèse globale rapide |
| GET | `/api/dashboard/statistics` | Métriques détaillées, répartition par catégorie et historique mensuel |
