# Multi-Stage Builds

## 🔄 Why Multi‑Stage Builds?

- **Builder stage**: contains compilers, build tools, dependencies → heavy.
- **Runtime stage**: contains only what’s needed to run → slim, secure.
- **Benefits**:
  - Smaller images (faster deploys, less attack surface).
  - Cleaner separation of build vs runtime.
  - Easier caching and reproducibility.

## 🦀 Go Microservice

```dockerfile
# Stage 1: Build
FROM golang:1.22 AS builder
WORKDIR /src
COPY . .
RUN go build -o app

# Stage 2: Runtime
FROM alpine:3.19
WORKDIR /app
COPY --from=builder /src/app .
EXPOSE 8080
CMD ["./app"]
```

**Nuance**: Go produces a single binary → runtime can be *tiny* (Alpine or even `scratch`).

## ☕ Java (Spring Boot)

```dockerfile
# Stage 1: Build
FROM maven:3.9.6-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn package -DskipTests

# Stage 2: Runtime
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=builder /app/target/myapp.jar .
EXPOSE 8080
CMD ["java", "-jar", "myapp.jar"]
```

**Nuance**: Java needs JRE at runtime, but not Maven. Multi‑stage removes build tools → final image much smaller.

## 🎨 React Frontend

```dockerfile
# Stage 1: Build
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: Runtime
FROM nginx:1.27-alpine
COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80
```

**Nuance**: Frontend apps don’t run Node in production. They serve static files → Nginx is faster, secure, and lightweight.

## 🐍 Python (Data Science App)

```dockerfile
# Stage 1: Build
FROM python:3.11-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip wheel --no-cache-dir --wheel-dir /wheels -r requirements.txt
COPY . .

# Stage 2: Runtime
FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /wheels /wheels
RUN pip install --no-cache /wheels/*
COPY . .
CMD ["python", "app.py"]
```

**Nuance**: Wheels (pre‑built packages) are cached in builder stage → runtime installs them quickly without compiling heavy libraries (like NumPy, Pandas).

| Language/App | Builder Stage | Runtime Stage | Benefit |
| --- | --- | --- | --- |
| **Go** | Compile binary | Tiny Alpine | Ultra‑small image |
| **Java** | Maven build | JRE only | Removes build tools |
| **React** | Node build | Nginx serve | Static assets only |
| **Python** | Build wheels | Slim runtime | Faster installs, smaller image |
