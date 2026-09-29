# chu_materiel

# CHU Matériel — Backend

API REST de gestion logistique du matériel pour le CHU.
Gestion des catégories, matériels, consommables, prêts, dons, services, utilisateurs et demandes.

## Stack technique

| Composant | Version |
|---|---|
| Node.js | 22 |
| Express | 5.1 |
| Prisma | 6.16 |
| PostgreSQL | 15+ |
| Auth | JWT + bcrypt |
| Génération QR | qrcode |

## Architecture

```
.
├── server.js              # Point d'entrée, montage des routes
├── routes/                # Définition des endpoints
├── controllers/           # Logique métier
├── Middleware/            # asyncHandler, auth…
├── prisma/
│   ├── schema.prisma      # Modèle de données
│   └── migrations/        # Historique
├── Dockerfile
├── Jenkinsfile
└── package.json
```

## Prérequis

- Node.js **22+**
- PostgreSQL **15+** (ou Docker)
- npm

## Installation locale

### 1. Cloner le repo

```bash
git clone https://github.com/Hinshart/refonte_backend_materiel_chu.git
cd refonte_backend_materiel_chu
```

### 2. Installer les dépendances

```bash
npm install
```

### 3. Configurer l'environnement

```bash
cp .env.example .env
# Éditer .env avec tes vraies valeurs
```

### 4. Lancer PostgreSQL (optionnel via Docker)

```bash
docker run --name chu-postgres \
  -e POSTGRES_USER=chu \
  -e POSTGRES_PASSWORD=chu \
  -e POSTGRES_DB=chu_materiel \
  -p 5432:5432 -d postgres:16
```

### 5. Appliquer les migrations Prisma

```bash
npx prisma migrate dev
npx prisma generate
```

### 6. Démarrer le serveur

```bash
# Développement (nodemon + babel)
npm run dev

# Production
npm start
```

L'API tourne sur `http://localhost:1303`.

## Lancer avec Docker

```bash
docker build -t chu-backend .
docker run -p 9095:9095 --env-file .env chu-backend
```

## Variables d'environnement

Voir `.env.example`. Les principales :

| Variable | Description | Exemple |
|---|---|---|
| `PORT` | Port d'écoute | `1303` |
| `DATABASE_URL` | URL PostgreSQL | `postgresql://user:pass@host:5432/db` |
| `JWT_SECRET` | Secret de signature JWT | chaîne longue et aléatoire |
| `SALT_ROUNDS` | Rounds bcrypt | `10` |
| `CORS_ORIGIN` | Origines autorisées (séparées par virgule) | `http://localhost:5173` |

## Endpoints principaux

Base URL : `/api`

| Route | Description |
|---|---|
| `POST /api/login` | Connexion (pseudo + mot de passe) |
| `POST /api/login/cin` | Connexion par CIN |
| `POST /api/login/update_account` | Mise à jour du compte |
| `/api/categorie` | Gestion des catégories |
| `/api/classe` | Gestion des classes |
| `/api/materiel` | Gestion des matériels |
| `/api/lot` | Gestion des lots |
| `/api/consommable` | Gestion des consommables |
| `/api/stock` | Gestion du stock |
| `/api/pret` | Gestion des prêts |
| `/api/donneur` | Gestion des donneurs |
| `/api/service` | Gestion des services |
| `/api/personne` | Gestion des personnes |
| `/api/compte` | Gestion des comptes |
| `/api/demande` | Gestion des demandes |
| `/api/inventaire` | Inventaire |
| `/api/dash` | Données du dashboard |
| `/api/distribution` | Distribution |
| `/api/notification` | Notifications |

## Rôles utilisateurs

- `DIRECTEUR`
- `RESPONSABLE`
- `SERVICE`
- `SERVICE_VUE`

## Base de données — aperçu du schéma

- **Categorie → Classe → Materiels / Consommable** : hiérarchie du matériel
- **Donneur → Lot_materiels / Lot_consommable** : traçabilité des dons
- **Service → Personne → Compte** : gestion des utilisateurs
- **Demande** : statuts `EN_ATTENTE`, `REFUSEE`, `TRAITEE`
- **Pret**, **Notification**, **Depense** : mouvements et suivi

Schéma complet : `prisma/schema.prisma`.

## Sécurité

- Mots de passe hashés avec **bcrypt** (`SALT_ROUNDS`)
- Authentification par **JWT** (durée : 12h)
- CORS restreint aux origines déclarées dans `CORS_ORIGIN`

Privé — projet CHU.