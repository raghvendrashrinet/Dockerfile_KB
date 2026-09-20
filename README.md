# Dockerfile Mastery

## Module 1: Foundations

### What is Docker & Dockerfile

- Purpose of containerization
- Anatomy of a Dockerfile (`FROM`, `RUN`, `CMD`, `ENTRYPOINT`, `COPY`, `WORKDIR`)

### Hands-on

- Write a simple Dockerfile for a Python "Hello World" app
- Build and run the image locally

---

## Module 2: Layering & Best Practices

### Concepts

- Image layers and caching
- Difference between `RUN`, `CMD`, and `ENTRYPOINT`
- Using `.dockerignore`

### Hands-on

- Optimize a Node.js Dockerfile by reducing layers
- Compare image sizes before and after optimization

---

## Module 3: Multi-Language Codebases

### Examples

- Python Flask API
- Node.js React frontend
- Java Spring Boot service

### Hands-on

- Write Dockerfiles for each
- Learn language-specific optimizations (`pip` cache, `npm ci`, Maven wrapper)

---

## Module 4: Multi-Stage Builds

### Concepts

- Builder vs runtime images
- Reducing final image size
- Security benefits

### Hands-on

- Build a **Go microservice**:
  - Stage 1: Compile binary
  - Stage 2: Copy binary into `scratch`/`alpine`
- Build a **React app**:
  - Stage 1: `npm build`
  - Stage 2: Serve static files with Nginx

---

## Module 5: Production-Grade Techniques

### Concepts

- Non-root users (`USER`)
- Health checks (`HEALTHCHECK`)
- Environment variables (`ENV`)
- Secrets & configs

### Hands-on

- Harden a Dockerfile with non-root user
- Add health checks for a web service
- Inject secrets via Docker/Kubernetes

---

## Module 6: Advanced Optimizations

### Concepts

- Alpine vs Debian images
- Slim images
- Caching strategies
- Reproducible builds

### Hands-on

- Convert a Java app from `openjdk:latest` to `openjdk:17-slim`
- Measure image size reduction

---

## Module 7: Real-World Implementations

### Microservices

- Frontend, backend, database containers

### CI/CD Integration

- Dockerfile in GitHub Actions
- Push to registry (DockerHub, ACR, ECR)

### Hands-on

- Write Dockerfiles for a full stack app
- Automate build & deploy pipeline

---

## Module 8: Debugging & Profiling

### Concepts

- Inspecting images (`docker history`, `docker inspect`)
- Debugging builds
- Profiling container performance

### Hands-on

- Use `docker exec` to debug running containers
- Profile memory usage in a containerized app

---

## Module 9: Enterprise & World-Class Practices

### Concepts

- Image signing & verification
- SBOM (Software Bill of Materials)
- Vulnerability scanning (Trivy, Anchore)

### Hands-on

- Scan your production image
- Generate SBOM for compliance   
