## How Docker Caching Works
- Every instruction (FROM, RUN, COPY, etc.) creates a layer.

- When you rebuild an image, Docker checks if a layer has changed:

  - If unchanged, Docker reuses the cached layer.

  - If changed, Docker rebuilds that layer and all layers after it.

This means the order of instructions in your Dockerfile directly affects build speed.

#### 🧩 Example: Node.js API
```dockerfile
FROM node:20-alpine
WORKDIR /app

# Copy dependency files first
COPY package*.json ./
RUN npm ci --only=production

# Copy source code later
COPY . .

EXPOSE 3000
CMD ["node", "server.js"]
```
Why this order matters:
- If you change only your app code (server.js), Docker reuses the cached npm ci layer → build is fast.

- If you change package.json, Docker invalidates the cache → dependencies are reinstalled.

#### 🐍 Example: Python Flask
```dockerfile
FROM python:3.11-slim
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000
CMD ["python", "app.py"]
```
Why this order matters:
 - requirements.txt copied first → dependency installation cached.

 - If you only change app.py, Docker skips reinstalling dependencies.

☕ Example: Java (Spring Boot)
```dockerfile
FROM maven:3.9.6-eclipse-temurin-17 AS builder
WORKDIR /app

COPY pom.xml .
RUN mvn dependency:go-offline

COPY src ./src
RUN mvn package -DskipTests
```
Why this order matters:
- Copying pom.xml first → Maven downloads dependencies once and caches them.

- If you only change source code, Docker reuses cached dependencies.

#### 🦀 Example: Go
```dockerfile
FROM golang:1.22 AS builder
WORKDIR /src

COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN go build -o app
```
Why this order matters:
- Copying go.mod and go.sum first → dependencies cached.

- If you only change .go files, Docker skips re‑downloading modules.

#### 🎯 Best Practices for Caching
- Copy dependency files first (package.json, requirements.txt, pom.xml, go.mod).

- Install dependencies before copying source code.

- Use `.dockerignore` to avoid copying unnecessary files`(logs, node_modules, .git)`.

- Pin base images (python:3.11-slim, not python:latest) → ensures reproducibility.

- Keep layers small and ordered logically.
