# Intelligent Virtual Art Gallery – Docker & Cloud Project

A web-based virtual art gallery built with **Next.js, React, TypeScript, PostgreSQL, and Prisma ORM**, with a containerized development environment using **Docker and Docker Compose**.

The application allows users to explore artworks, manage personal favorites, and receive personalized recommendations based on shared artwork tags. It also provides role-based access control for administrators and curators, enabling different levels of artwork management.

The project focuses on combining a full-stack web application with containerization, database services, and a GitHub Actions CI workflow.

## Features

### User Authentication and Authorization
- User registration and login.
- Password hashing using `bcryptjs`.
- JWT-based authentication stored in HTTP-only cookies.
- Protected application routes and API endpoints.
- Role-based access control with three roles:
  - **USER** – browse artworks, manage favorites, and view recommendations.
  - **CURATOR** – create and edit artworks.
  - **ADMIN** – delete artworks.

### Virtual Art Gallery
- Browse a collection of artworks.
- View artwork information, including title, description, artist, year, and category.
- Search artworks by title and description.
- Paginated artwork loading.
- Artwork image preview through an enlarged image overlay.

### Favorites
- Add artworks to or remove them from a personal favorites collection.
- View all saved favorite artworks.
- Store favorite relationships in the PostgreSQL database.

### Personalized Recommendations
- Generate recommendations based on tags from the user's favorite artworks.
- Calculate recommendation scores by matching artwork tags.
- Exclude artworks that the user has already favorited.
- Display stronger matches separately from additional suggestions.

The recommendation functionality uses a tag-based scoring algorithm rather than an external AI service or machine-learning model.

### Artwork Management
- Curators can create new artworks and edit existing entries.
- Administrators can delete artworks.
- Role permissions are checked on the server-side API endpoints.

## Technology Stack

| Technology | Purpose |
|---|---|
| Next.js 16 | Full-stack React framework |
| React 19 | User interface |
| TypeScript | Static typing |
| PostgreSQL 15 | Relational database |
| Prisma ORM 6 | Database access and schema management |
| Docker | Application containerization |
| Docker Compose | Multi-container environment orchestration |
| JWT / jose | Authentication and token verification |
| bcryptjs | Password hashing |
| Tailwind CSS 4 | Styling |
| GitHub Actions | Continuous integration workflow |

## Docker Architecture

The project uses Docker Compose to coordinate the application and its supporting services.

The environment consists of three main services:

- **App** – runs the Next.js application on port `3000`.
- **PostgreSQL** – provides the database service on port `5432`, with persistent storage through a Docker volume.
- **Prisma Studio** – provides a visual interface for inspecting and managing database records on port `5555`.

Docker Compose also configures an application network, service dependencies, and health checks.

### Container Configuration

The repository includes:
- A multi-stage `Dockerfile` with separate build and runtime stages.
- A `docker-compose.yml` file defining the application services.
- A `.dockerignore` file to exclude unnecessary files from the Docker build context.
- A persistent PostgreSQL volume to retain database data.

## Getting Started

### Prerequisites

Make sure the following tools are installed:

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) or Docker Engine
- Docker Compose
- Git

### 1. Clone the Repository

```bash
git clone https://github.com/aleksandra-milikic/cloud-projekat-ai_virtuelna_galerija_2025_3273---docker.git
cd cloud-projekat-ai_virtuelna_galerija_2025_3273-docker
```

### 2. Configure Environment Variables

Create a `.env` file in the project root directory.

Configure the PostgreSQL connection and authentication secret:

```env
POSTGRES_USER=postgres
POSTGRES_PASSWORD=your_password
POSTGRES_DB=virtual_gallery

DATABASE_URL="postgresql://postgres:your_password@postgres:5432/virtual_gallery?schema=public"

JWT_SECRET=your_secure_jwt_secret
```

Use the same PostgreSQL credentials in the `POSTGRES_*` variables and the `DATABASE_URL` connection string.

Replace the example password and JWT secret with your own values. Do not commit your `.env` file to the repository.

### 3. Start the Docker Environment

Build the application image and start all services:

```bash
docker compose up --build
```

The services will be available at:

| Service | Address |
|---|---|
| Next.js Application | http://localhost:3000 |
| PostgreSQL | localhost:5432 |
| Prisma Studio | http://localhost:5555 |

### 4. Initialize the Database

After the containers are running, generate the Prisma Client and apply the database migrations:

```bash
docker compose exec app npx prisma generate
docker compose exec app npx prisma migrate deploy
```

If the repository does not contain migration files, create the database schema using:

```bash
docker compose exec app npx prisma db push
```

### 5. Stop the Environment

To stop the running containers:

```bash
docker compose down
```

To stop the containers and remove the persistent PostgreSQL volume:

```bash
docker compose down -v
```

**Warning:** Removing the volume deletes the database data stored in it.

## Database Schema

The application uses Prisma ORM with PostgreSQL.

The main database models include:

- **User** – stores user information, hashed passwords, and assigned roles.
- **Artwork** – stores artwork details, image URLs, metadata, and tags.
- **Gallery** – represents gallery collections.
- **Exhibition** – stores exhibition information and its active status.
- **Favorite** – connects users with their favorite artworks.

The database also defines a `Role` enum with the values `USER`, `ADMIN`, and `CURATOR`.

## API Overview

The application exposes Next.js API routes for authentication and artwork management.

| Endpoint | Method | Description |
|---|---|---|
| `/api/auth/register` | POST | Register a new user |
| `/api/auth/login` | POST | Authenticate a user |
| `/api/auth/logout` | POST | Log out and clear the authentication cookie |
| `/api/auth/me` | GET | Retrieve the currently authenticated user |
| `/api/artworks` | GET | Retrieve paginated artworks |
| `/api/artworks` | POST | Create an artwork |
| `/api/artworks/[id]` | GET | Retrieve a specific artwork |
| `/api/artworks/[id]` | PUT | Update an artwork |
| `/api/artworks/[id]` | DELETE | Delete an artwork |
| `/api/favorites` | GET | Retrieve the user's favorites |
| `/api/favorites` | POST | Add or remove a favorite |
| `/api/recommendations` | GET | Retrieve personalized recommendations |

## Continuous Integration

The repository includes a GitHub Actions workflow located in:

```text
.github/workflows/ci.yml
```

The workflow is triggered by:
- Pushes to the `main` branch.
- Pull requests targeting the `main` branch.
- Manual execution through `workflow_dispatch`.

The CI pipeline includes:
- Installing Node.js 20.
- Installing project dependencies.
- Generating the Prisma Client.
- Building the Next.js application.
- Running additional workflow jobs.

This workflow provides an automated build validation process for the project.

## Project Structure

```text
.
├── .github/
│   └── workflows/
│       └── ci.yml
├── prisma/
│   └── schema.prisma
├── public/
├── src/
│   ├── app/
│   │   ├── api/
│   │   ├── dashboard/
│   │   ├── favorites/
│   │   ├── gallery/
│   │   ├── login/
│   │   ├── recommendations/
│   │   └── register/
│   ├── components/
│   └── lib/
├── .dockerignore
├── Dockerfile
├── docker-compose.yml
├── middleware.ts
├── next.config.ts
├── package.json
└── tsconfig.json
```

## License

This project was developed as part of a university course project.
