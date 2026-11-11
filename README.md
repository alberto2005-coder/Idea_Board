# IdeaBoard

[Español](#español) · [English](#english)

---

## Español

### Índice

- [Descripción](#descripción)
- [Características](#características)
- [Tecnologías](#tecnologías)
- [Requisitos](#requisitos)
- [Instalación](#instalación)
- [Configuración de la base de datos](#configuración-de-la-base-de-datos)
- [Ejecución](#ejecución)
- [API](#api)
- [Scripts](#scripts)
- [Estructura del proyecto](#estructura-del-proyecto)
- [Contribuidores](#contribuidores)
- [Licencia](#licencia)

### Descripción

**IdeaBoard** es una aplicación full-stack para publicar ideas, comentarlas y votarlas. El backend está construido con **Express**, **Drizzle ORM** y **PostgreSQL**, y el cliente con **React 18**, **Vite**, **TanStack Query** y componentes de **shadcn/ui** sobre Radix.

Cada idea tiene autor, título y contenido; se puede votar a favor o en contra (un voto por usuario e idea) y comentarla. La pantalla de estadísticas resume la actividad.

### Características

- **Publicar ideas** con título, contenido y autor.
- **Votos** `up` / `down` con restricción `UNIQUE(idea_id, username)`.
- **Comentarios** asociados a cada idea.
- **Estadísticas** agregadas en `/api/statistics`.
- **Validación con Zod** de todas las peticiones antes de tocar la base de datos.
- **Claves foráneas con `ON DELETE CASCADE`**: al borrar una idea se borran sus votos y comentarios.
- **SPA con React Router** y estado del servidor con TanStack Query.

### Tecnologías

| Capa | Tecnología |
|---|---|
| Frontend | React 18 + TypeScript + Vite 5 |
| UI | Tailwind CSS + shadcn/ui (Radix) + lucide-react |
| Estado | TanStack Query + Wouter |
| Backend | Express 4 + TypeScript (`tsx`) |
| ORM | Drizzle ORM + drizzle-zod |
| Base de datos | PostgreSQL 14+ |
| Validación | Zod |

### Requisitos

- **Node.js 22.23.4+, 24.22.1+ o 26.12.1+**. El repo incluye `.nvmrc` y `.npmrc` con `engine-strict`, así que `npm install` falla con otras versiones. En Replit, `.replit` declara el módulo `nodejs-24`.
- npm.
- **PostgreSQL 14 o superior** en marcha.
- Git (opcional).

### Instalación

```bash
git clone https://github.com/NekeritStudio/Idea_Board.git
cd Idea_Board
npm install
```

Copia el archivo de ejemplo y ajusta tus credenciales:

```bash
cp .env.example .env
```

```env
DATABASE_URL=postgresql://usuario:password@localhost:5432/idea_board
PORT=5000
```

> `.env` no se versiona: está excluido por `.gitignore` porque contiene credenciales. Usa siempre `.env.example` como plantilla.

### Configuración de la base de datos

**1. Crear la base de datos y el usuario**

```sql
CREATE DATABASE idea_board;
CREATE USER idea_user WITH PASSWORD 'tu_password';
GRANT ALL PRIVILEGES ON DATABASE idea_board TO idea_user;
```

**2. Crear las tablas**

Puedes generarlas automáticamente con Drizzle:

```bash
npm run db:push
```

O crearlas a mano:

```sql
CREATE TABLE ideas (
    id SERIAL PRIMARY KEY,
    author VARCHAR(100) NOT NULL,
    title VARCHAR(255) NOT NULL,
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL
);

CREATE TABLE votes (
    id SERIAL PRIMARY KEY,
    idea_id INTEGER REFERENCES ideas(id) ON DELETE CASCADE NOT NULL,
    username VARCHAR(100) NOT NULL,
    vote_type VARCHAR(10) NOT NULL CHECK (vote_type IN ('up', 'down')),
    UNIQUE (idea_id, username)
);

CREATE TABLE comments (
    id SERIAL PRIMARY KEY,
    idea_id INTEGER REFERENCES ideas(id) ON DELETE CASCADE NOT NULL,
    author VARCHAR(100) NOT NULL,
    text TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL
);
```

### Ejecución

```bash
npm run dev
```

El servidor queda en <http://localhost:5000> (o el puerto que definas en `PORT`).

### API

| Método | Endpoint | Descripción |
|---|---|---|
| `GET` | `/api/ideas` | Lista todas las ideas |
| `POST` | `/api/ideas` | Crea una idea (`title`, `content`, `author`) |
| `POST` | `/api/vote` | Vota una idea (`ideaId`, `username`, `voteType`) |
| `GET` | `/api/comments/:ideaId` | Comentarios de una idea |
| `POST` | `/api/comments` | Crea un comentario (`ideaId`, `text`, `author`) |
| `GET` | `/api/statistics` | Estadísticas agregadas |

Todas las escrituras se validan con los esquemas Zod de `shared/schema.ts`; si el cuerpo no encaja, la API responde `400` con el detalle de los errores.

### Scripts

| Script | Función |
|---|---|
| `npm run dev` | Servidor de desarrollo con recarga en caliente |
| `npm run build` | Build del cliente (Vite) y del servidor (esbuild) |
| `npm start` | Ejecuta el build en modo producción |
| `npm run check` | Comprobación de tipos con TypeScript |
| `npm run db:push` | Aplica el esquema de Drizzle a PostgreSQL |

### Estructura del proyecto

```text
Idea_Board/
├── client/
│   └── src/
│       ├── components/          # idea-card, idea-form, statistics, ui/
│       ├── pages/               # home, not-found
│       └── main.tsx
├── server/
│   ├── db.ts                    # Conexión con Drizzle
│   ├── storage.ts               # Acceso a ideas, votos y comentarios
│   ├── routes.ts                # Endpoints de la API
│   ├── vite.ts                  # Integración con Vite en desarrollo
│   └── index.ts                 # Entrada del servidor
├── shared/
│   └── schema.ts                # Tablas Drizzle + esquemas Zod
├── drizzle.config.ts
├── package.json
├── .env.example
└── README.md
```

### Contribuidores

- [Kennet Rodriguez](https://github.com/Kennetrl)
- [Hugo](https://github.com/hugooae)
- [Alberto](https://github.com/alberto2005-coder)

### Licencia

**MIT License © 2025**, según se declara en `package.json`. Repositorio original: [NekeritStudio/Idea_Board](https://github.com/NekeritStudio/Idea_Board).

---

## English

### Table of contents

- [Overview](#overview)
- [Features](#features)
- [Tech stack](#tech-stack)
- [Requirements](#requirements)
- [Installation](#installation)
- [Database setup](#database-setup)
- [Running the app](#running-the-app)
- [API](#api)
- [Scripts](#scripts)
- [Project structure](#project-structure)
- [Contributors](#contributors)
- [License](#license)

### Overview

**IdeaBoard** is a full-stack app to post ideas, comment on them and vote. The backend is built with **Express**, **Drizzle ORM** and **PostgreSQL**; the client with **React 18**, **Vite**, **TanStack Query** and **shadcn/ui** components on top of Radix.

Each idea has an author, a title and content; it can be upvoted or downvoted (one vote per user and idea) and commented on. A statistics screen summarizes activity.

### Features

- **Post ideas** with title, content and author.
- **Votes** `up` / `down` with a `UNIQUE(idea_id, username)` constraint.
- **Comments** attached to each idea.
- **Statistics** aggregated at `/api/statistics`.
- **Zod validation** for every request before touching the database.
- **Foreign keys with `ON DELETE CASCADE`**: deleting an idea removes its votes and comments.
- **React Router SPA** with server state handled by TanStack Query.

### Tech stack

| Layer | Technology |
|---|---|
| Frontend | React 18 + TypeScript + Vite 5 |
| UI | Tailwind CSS + shadcn/ui (Radix) + lucide-react |
| State | TanStack Query + Wouter |
| Backend | Express 4 + TypeScript (`tsx`) |
| ORM | Drizzle ORM + drizzle-zod |
| Database | PostgreSQL 14+ |
| Validation | Zod |

### Requirements

- **Node.js 22.23.4+, 24.22.1+ or 26.12.1+**. The repo ships `.nvmrc` and `.npmrc` with `engine-strict`, so `npm install` fails on other versions. On Replit, `.replit` declares the `nodejs-24` module.
- npm.
- **PostgreSQL 14 or later** running.
- Git (optional).

### Installation

```bash
git clone https://github.com/NekeritStudio/Idea_Board.git
cd Idea_Board
npm install
```

Copy the example file and set your credentials:

```bash
cp .env.example .env
```

```env
DATABASE_URL=postgresql://user:password@localhost:5432/idea_board
PORT=5000
```

> `.env` is not versioned: `.gitignore` excludes it because it holds credentials. Always start from `.env.example`.

### Database setup

**1. Create the database and user**

```sql
CREATE DATABASE idea_board;
CREATE USER idea_user WITH PASSWORD 'your_password';
GRANT ALL PRIVILEGES ON DATABASE idea_board TO idea_user;
```

**2. Create the tables**

You can let Drizzle generate them:

```bash
npm run db:push
```

Or create them by hand:

```sql
CREATE TABLE ideas (
    id SERIAL PRIMARY KEY,
    author VARCHAR(100) NOT NULL,
    title VARCHAR(255) NOT NULL,
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL
);

CREATE TABLE votes (
    id SERIAL PRIMARY KEY,
    idea_id INTEGER REFERENCES ideas(id) ON DELETE CASCADE NOT NULL,
    username VARCHAR(100) NOT NULL,
    vote_type VARCHAR(10) NOT NULL CHECK (vote_type IN ('up', 'down')),
    UNIQUE (idea_id, username)
);

CREATE TABLE comments (
    id SERIAL PRIMARY KEY,
    idea_id INTEGER REFERENCES ideas(id) ON DELETE CASCADE NOT NULL,
    author VARCHAR(100) NOT NULL,
    text TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL
);
```

### Running the app

```bash
npm run dev
```

The server listens on <http://localhost:5000> (or whatever `PORT` you set).

### API

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/api/ideas` | List all ideas |
| `POST` | `/api/ideas` | Create an idea (`title`, `content`, `author`) |
| `POST` | `/api/vote` | Vote on an idea (`ideaId`, `username`, `voteType`) |
| `GET` | `/api/comments/:ideaId` | Comments for an idea |
| `POST` | `/api/comments` | Create a comment (`ideaId`, `text`, `author`) |
| `GET` | `/api/statistics` | Aggregated statistics |

Every write is validated against the Zod schemas in `shared/schema.ts`; a bad body returns `400` with the error details.

### Scripts

| Script | Purpose |
|---|---|
| `npm run dev` | Development server with hot reload |
| `npm run build` | Builds the client (Vite) and the server (esbuild) |
| `npm start` | Runs the production build |
| `npm run check` | TypeScript type check |
| `npm run db:push` | Applies the Drizzle schema to PostgreSQL |

### Project structure

```text
Idea_Board/
├── client/
│   └── src/
│       ├── components/          # idea-card, idea-form, statistics, ui/
│       ├── pages/               # home, not-found
│       └── main.tsx
├── server/
│   ├── db.ts                    # Drizzle connection
│   ├── storage.ts               # Ideas, votes and comments data access
│   ├── routes.ts                # API endpoints
│   ├── vite.ts                  # Vite integration in development
│   └── index.ts                 # Server entry point
├── shared/
│   └── schema.ts                # Drizzle tables + Zod schemas
├── drizzle.config.ts
├── package.json
├── .env.example
└── README.md
```

### Contributors

- [Kennet Rodriguez](https://github.com/Kennetrl)
- [Hugo](https://github.com/hugooae)
- [Alberto](https://github.com/alberto2005-coder)

### License

**MIT License © 2025**, as declared in `package.json`. Original repository: [NekeritStudio/Idea_Board](https://github.com/NekeritStudio/Idea_Board).
