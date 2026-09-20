##  full stack microservices example
### frontend (React + Nginx), backend (Node/Java/Python), and database (Postgres)

- `Frontend` → React app served by Nginx
- `Backend` → Node.js API
- `Database` → PostgreSQL (official image)

#### 🎨 Frontend (React + Nginx)
Dockerfile:
```dockerfile
# Stage 1: Build React app
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:1.27-alpine
COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80

```
Nuance: Node is only used to build assets. Nginx serves static files in production.

#### 🟢 Backend (Node.js API)
Dockerfile:
```dockerfile
FROM node:20-alpine
WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

ENV NODE_ENV=production
ENV PORT=3000

EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=5s \
  CMD curl -f http://localhost:3000/health || exit 1

CMD ["node", "server.js"]
```
Nuance: Hardened with non‑root user, environment variables, and health check.

#### 🗄️ Database (PostgreSQL)
Dockerfile:
```dockerfile
# Use official image — no need to build custom
FROM postgres:16-alpine

ENV POSTGRES_USER=appuser
ENV POSTGRES_PASSWORD=securepassword
ENV POSTGRES_DB=mydb

EXPOSE 5432
```
Nuance: For databases, you usually rely on official images and configure via environment variables.

#### ⚙️ Docker Compose (Orchestration)
```yaml
version: "3.9"
services:
  frontend:
    build: ./frontend
    ports:
      - "8080:80"

  backend:
    build: ./backend
    ports:
      - "3000:3000"
    environment:
      - DB_HOST=db
      - DB_USER=appuser
      - DB_PASSWORD=securepassword
      - DB_NAME=mydb
    depends_on:
      - db

  db:
    build: ./db
    volumes:
      - db_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  db_data:
```   q
