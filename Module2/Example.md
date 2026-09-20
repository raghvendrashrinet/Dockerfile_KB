## 🟢 Example 1: Node.js (Express API)
Dockerfile:

```dockerfile
FROM node:20-alpine
WORKDIR /app
# Copy only package.json first for caching
COPY package*.json ./
RUN npm ci --only=production

# Copy rest of the code
COPY . .

EXPOSE 3000
CMD ["node", "server.js"]
```

### Explanation
 - FROM node:20-alpine → Start from a lightweight Node.js image (Alpine Linux). Smaller = faster, but sometimes missing libraries.
 - WORKDIR /app → Sets the working directory inside the container. All commands run here.
 - COPY package*.json ./ → Copies only dependency files first. This allows Docker to cache the npm ci step if code changes but dependencies don’t.
 - RUN npm ci --only=production → Installs dependencies in a clean, reproducible way. ci is better than install for CI/CD.
 - COPY . . → Copies the rest of the source code.
 - EXPOSE 3000 → Documents that the app listens on port 3000.
 - CMD ["node", "server.js"] → Defines the default command to run when the container starts.

### ☕ Example 2: Java (Spring Boot)
Dockerfile:

```dockerfile
# Build stage
FROM maven:3.9.6-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn package -DskipTests

# Runtime stage
FROM eclipse-temurin:17-jre
WORKDIR /app
COPY --from=builder /app/target/myapp.jar .
EXPOSE 8080
CMD ["java", "-jar", "myapp.jar"]
```
### Explanation
  **Stage 1 (builder):**
 - FROM maven:... AS builder → Uses Maven with JDK to compile the app.
 - WORKDIR /app → Working directory.
 - COPY pom.xml . → Copy Maven config first (caching trick).
 - COPY src ./src → Copy source code.
 - RUN mvn package -DskipTests → Build the JAR file.

**Stage 2 (runtime):**

- FROM eclipse-temurin:17-jre → Lightweight JRE image (no compiler).
- COPY --from=builder ... → Copies only the built JAR from stage 1.
- EXPOSE 8080 → Spring Boot default port.
- CMD ["java", "-jar", "myapp.jar"] → Runs the app.

👉 Multi‑stage build keeps the final image small (no Maven, no source code, just the JAR).

### 🐍 Example 3: Python (Flask)
Dockerfile:

```dockerfile
FROM python:3.11-slim
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000
CMD ["python", "app.py"]
```
### Explanation
- FROM python:3.11-slim → Slim Python image (smaller than full Debian).
- WORKDIR /app → Working directory.
- COPY requirements.txt . → Copy dependency list first.
- RUN pip install --no-cache-dir -r requirements.txt → Install dependencies without caching wheels (reduces image size).
- COPY . . → Copy app code.
- EXPOSE 5000 → Flask default port.
- CMD ["python", "app.py"] → Runs the app.

### 🦀 Example 4: Go (Microservice)
Dockerfile:

```dockerfile
# Build stage
FROM golang:1.22 AS builder
WORKDIR /src
COPY . .
RUN go build -o app

# Runtime stage
FROM alpine:3.19
WORKDIR /app
COPY --from=builder /src/app .
EXPOSE 8080
CMD ["./app"]
```
### Explanation
**Stage 1 (builder):**
- FROM golang:1.22 AS builder → Full Go environment to compile code.
- WORKDIR /src → Working directory.
- COPY . . → Copy source code.
- RUN go build -o app → Compiles Go binary.

**Stage 2 (runtime):**
- FROM alpine:3.19 → Tiny Linux image.
- COPY --from=builder /src/app . → Copy compiled binary only.
- EXPOSE 8080 → Service port.
- CMD ["./app"] → Run the binary.

👉 Final image is just a few MB — perfect for microservices.

### 🧩 Key Takeaways
 - `Node/Python` → focus on caching dependencies.
 - `Java/Go` → multi‑stage builds are essential (compile → runtime).
 - `Alpine vs Slim` → trade‑off between size and compatibility.
 - `EXPOSE & CMD` → define how the container runs and communicates.
