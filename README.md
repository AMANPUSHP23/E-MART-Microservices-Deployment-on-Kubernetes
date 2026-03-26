# E-MART: Microservices Deployment on Kubernetes

<p align="center">
  <strong>Multi-service e-commerce platform with Angular, Node.js, Spring Boot, Docker, Jenkins, NGINX, and Helm.</strong>
</p>

<p align="center">
  <img alt="Angular" src="https://img.shields.io/badge/Frontend-Angular%2012-C62828?style=for-the-badge&logo=angular&logoColor=white">
  <img alt="Node" src="https://img.shields.io/badge/API-Node.js%20%2B%20Express-1B5E20?style=for-the-badge&logo=node.js&logoColor=white">
  <img alt="Spring Boot" src="https://img.shields.io/badge/Books%20API-Spring%20Boot%202.3-2E7D32?style=for-the-badge&logo=springboot&logoColor=white">
  <img alt="MongoDB" src="https://img.shields.io/badge/Database-MongoDB-0B6E4F?style=for-the-badge&logo=mongodb&logoColor=white">
  <img alt="MySQL" src="https://img.shields.io/badge/Database-MySQL%208-1565C0?style=for-the-badge&logo=mysql&logoColor=white">
  <img alt="Docker" src="https://img.shields.io/badge/Runtime-Docker-0277BD?style=for-the-badge&logo=docker&logoColor=white">
</p>

<p align="center">
  <em>A full-stack storefront that combines customer shopping flows, cart and order management, authentication, and a separate books service behind one gateway.</em>
</p>

---

## Why This Project Stands Out

E-MART is not a simple monolith. It is a small platform composed of:

- An Angular storefront in [`client/`](./client)
- A Node/Express commerce API in [`nodeapi/`](./nodeapi)
- A Spring Boot books API in [`javaapi/`](./javaapi)
- An NGINX reverse proxy in [`nginx/`](./nginx)
- Docker and Docker Compose orchestration at the repository root
- A Helm chart in [`kkartchart/`](./kkartchart)
- Jenkins pipelines for image build and Kubernetes deployment

The frontend uses relative API paths (`/api`, `/webapi`), which allows the reverse proxy to present the system as a single application entrypoint.

---

## Architecture View

```mermaid
flowchart LR
    U[User Browser] --> N[NGINX Gateway :80]
    N --> C[Angular Client :4200]
    N --> A[Node API :5000]
    N --> B[Spring Boot Books API :9000]
    A --> M[(MongoDB :27017)]
    B --> Y[(MySQL :3306)]
```

### Traffic Map

| Route | Target Service | Purpose |
|---|---|---|
| `/` | Angular client | Storefront UI |
| `/api/*` | Node API | Auth, products, categories, cart, orders |
| `/webapi/*` | Spring Boot API | Books CRUD endpoints |

---

## Project Evaluation

### Current Strengths

- Clear service separation between UI, commerce API, books API, gateway, and databases
- Containerized delivery across all major services
- Helm chart included for Kubernetes deployment
- Jenkins pipeline already wired for image publishing and Helm upgrades
- Frontend API usage is proxy-friendly because it relies on `/api` and `/webapi`

### Current Gaps Worth Fixing

- Secrets are hardcoded in source today, including database/JWT config and email credentials
- Runtime versions are dated: Node 14, Angular 12, Java 8, Spring Boot 2.3
- `docker-compose.yaml` has no persistent volumes, health checks, or environment externalization
- The root-level `Dockerfile` is different from the service-specific Dockerfiles and can confuse deployment intent
- There is little visible automated test coverage beyond the generated Spring Boot test and Angular defaults

### Overall Assessment

This is a solid portfolio-style DevOps/full-stack project with real deployment structure, not just CRUD screens. The biggest step to production-readiness is configuration hardening: move secrets to environment variables or secret managers, add health probes, and modernize runtime versions.

---

## Service Breakdown

| Service | Folder | Port | Stack | Backing Store |
|---|---|---:|---|---|
| Frontend | [`client/`](./client) | `4200` | Angular 12 + Bootstrap | Calls proxy routes |
| Commerce API | [`nodeapi/`](./nodeapi) | `5000` | Node.js + Express + Mongoose | MongoDB |
| Books API | [`javaapi/`](./javaapi) | `9000` | Spring Boot + Spring Data JPA | MySQL |
| Gateway | [`nginx/`](./nginx) | `80` | NGINX | Routes requests |
| Helm Chart | [`kkartchart/`](./kkartchart) | n/a | Kubernetes packaging | K8s deployment |

---

## Feature Snapshot

### Storefront

- Landing page with promotional sections and product highlights
- Login and registration flows
- Protected dashboard route for authenticated users
- Product search, filtering, and category browsing
- Cart flow and order placement
- Book module connected to the Java service

### Node Commerce API

- User registration and login with JWT-based auth
- Product CRUD with image upload support
- Category CRUD and category-linked product retrieval
- Cart management per user
- Order creation and shipping-date validation
- Current user and order history endpoints

### Spring Boot Books API

- List all books
- Search books by title
- Fetch book by ID
- Create, update, and delete books
- Fetch published books

---

## Repository Layout

```text
emartapp-main/
|-- client/                  # Angular storefront
|-- nodeapi/                 # Node/Express commerce backend
|-- javaapi/                 # Spring Boot books backend
|-- nginx/                   # Reverse proxy configuration
|-- kkartchart/              # Helm chart and K8s templates
|-- docker-compose.yaml      # Local multi-container orchestration
|-- Dockerfile               # Root multi-stage image build
|-- Jenkinsfile              # CI/CD pipeline for images + Helm
`-- intDockerAndCompose.txt  # Docker/Docker Compose setup notes
```

---

## Tech Stack

| Layer | Technologies |
|---|---|
| Frontend | Angular 12, TypeScript, Bootstrap, Font Awesome |
| Commerce Backend | Node.js, Express, Mongoose, JWT, Multer, Nodemailer |
| Books Backend | Java 8, Spring Boot 2.3, Spring Data JPA |
| Databases | MongoDB 4, MySQL 8.0.33 |
| Gateway | NGINX |
| Containers | Docker, Docker Compose |
| Delivery | Jenkins, Helm, Kubernetes |

---

## Local Development

### 1. Prerequisites

- Node.js 14.x recommended for parity with the Dockerfiles
- npm
- Java 8
- Maven
- Docker and Docker Compose

### 2. Run Services Individually

### Frontend

```bash
cd client
npm install
npm start
```

Opens the Angular app on `http://localhost:4200`.

### Node API

```bash
cd nodeapi
npm install
npm start
```

Runs the commerce API on `http://localhost:5000`.

Expected dependency:

- MongoDB available at `mongodb://emongo:27017/epoc` or adjusted in `nodeapi/config/keys.js`

### Spring Boot API

```bash
cd javaapi
mvn spring-boot:run
```

Runs the books API on `http://localhost:9000`.

Expected dependency:

- MySQL available at `emartdb:3306/books` or adjusted in `javaapi/src/main/resources/application.properties`

---

## Docker Compose Run

Use Docker Compose when you want the complete stack with gateway plus both databases.

```bash
docker compose up --build
```

Exposed services:

| URL | Description |
|---|---|
| `http://localhost/` | Main application via NGINX |
| `http://localhost:4200` | Frontend container direct access |
| `http://localhost:5000` | Node API direct access |
| `http://localhost:9000` | Spring API direct access |
| `http://localhost:27017` | MongoDB |
| `http://localhost:3306` | MySQL |

### Compose Topology

The root [`docker-compose.yaml`](./docker-compose.yaml) starts:

- `client`
- `api`
- `webapi`
- `nginx`
- `emongo`
- `emartdb`

---

## Container Files

| File | Role |
|---|---|
| [`client/Dockerfile`](./client/Dockerfile) | Builds Angular and serves it through NGINX on port `4200` |
| [`nodeapi/Dockerfile`](./nodeapi/Dockerfile) | Installs and runs the Express API on port `5000` |
| [`javaapi/Dockerfile`](./javaapi/Dockerfile) | Builds the JAR with Maven and runs Spring Boot on port `9000` |
| [`Dockerfile`](./Dockerfile) | Root multi-stage build combining UI artifacts with Node runtime |

Note: the repo currently contains both service-level Dockerfiles and a root aggregate Dockerfile. If this repository evolves further, it would be worth standardizing on one deployment strategy to reduce ambiguity.

---

## Reverse Proxy Design

[`nginx/default.conf`](./nginx/default.conf) routes traffic like this:

- `/` to the Angular client
- `/api` to the Node API
- `/webapi` to the Spring Boot books API

This is why the Angular services can safely call relative endpoints such as:

- `/api`
- `/webapi`

without hardcoding hostnames into production builds.

---

## API Surface

### Node API

Base path: `/api`

Representative routes:

- `POST /api/user/register`
- `POST /api/user/login`
- `GET /api/shop/info`
- `GET /api/shop/products`
- `POST /api/shop`
- `PUT /api/shop/cart/:userId/:productId`
- `POST /api/shop/orders/:userId`
- `GET /api/shop/current`

### Books API

Base path: `/webapi`

Representative routes:

- `GET /webapi/books`
- `GET /webapi/books/{id}`
- `POST /webapi/books`
- `PUT /webapi/books/{id}`
- `DELETE /webapi/books/{id}`
- `GET /webapi/books/published`

---

## CI/CD Pipeline

The root [`Jenkinsfile`](./Jenkinsfile) shows a deployment flow with:

- Docker image builds for `client`, `javaapi`, and `nodeapi`
- Pushes to a Nexus-style registry
- Helm upgrades against the `kkartchart` chart
- Change-based stages so only modified services rebuild and redeploy

This gives the project a strong DevOps story:

- Application code
- Container packaging
- Registry publish
- Kubernetes rollout

all in the same repository.

---

## Kubernetes and Helm

The Helm chart lives in [`kkartchart/`](./kkartchart) and is organized into:

- `charts/frontend`
- `charts/backend`
- `charts/database`

Configured service paths in the chart:

- Frontend path: `/`
- Main API path: `/api`
- Books API path: `/webapi`

Typical install command:

```bash
helm upgrade kubekart kkartchart --install --namespace kart
```

---

## Recommended Hardening Roadmap

If you want to improve this codebase beyond documentation, tackle these next:

1. Move JWT, database, and SMTP secrets out of source code.
2. Add `.env` support for local development and secret references for Kubernetes.
3. Add database volumes in Compose so state survives restarts.
4. Add health checks and readiness/liveness probes.
5. Upgrade Node, Angular, Java, and Spring Boot versions.
6. Add real backend and frontend test coverage for critical flows.
7. Remove duplicated or overlapping container build strategies.

---

## Quick Start Summary

```bash
git clone <your-repo-url>
cd emartapp-main
docker compose up --build
```

Then open:

```text
http://localhost/
```

---

## Final Note

This repository already demonstrates full-stack breadth:

- UI development
- API development
- Polyglot backend architecture
- Reverse proxying
- Containerization
- CI/CD
- Helm-based Kubernetes deployment

With secret management and runtime modernization, it can move from a strong learning/demo platform toward a more production-shaped implementation.
