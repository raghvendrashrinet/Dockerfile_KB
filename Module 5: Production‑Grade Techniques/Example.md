## 🔒 Security & Reliability Enhancements
#### 1. Run as Non‑Root User
By default, containers often run as root. That’s risky — if someone breaks out of the app, they get root access inside the container.
Instead, create a dedicated user.

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Switch to non-root
USER appuser

EXPOSE 3000
CMD ["node", "server.js"]
```
👉 Now the app runs as appuser, not root. Safer in production.

#### 2. Health Checks
Containers should report if they’re healthy. Docker/Kubernetes can then restart them if needed.

```dockerfile
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1
```
👉 This checks /health endpoint every 30s. If it fails, container is marked unhealthy.

#### 3. Environment Variables
Use ENV for configuration, not hard‑coding values.

```dockerfile
ENV NODE_ENV=production
ENV PORT=3000
```
👉 Keeps config flexible. In Kubernetes, you can override these easily.

#### 4. Secrets & Configs
Never bake secrets into Dockerfiles. Instead:

Use environment variables injected at runtime.

Or mount secrets via Kubernetes Secrets.

Bad ❌:

```dockerfile
ENV DB_PASSWORD=supersecret
```
Good ✅:

- Inject DB_PASSWORD at runtime (kubectl set env or Kubernetes Secret).

#### 5. Logging & Observability
Containers should log to stdout/stderr so orchestration tools (Kubernetes, Docker) can capture logs.

```js
console.log("App started on port", process.env.PORT);
```
👉 Avoid writing logs to files inside the container.

##### 🧩 Example: Hardened Node.js Dockerfile
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

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

CMD ["node", "server.js"]
```
🎯 What You’ve Learned
  - Non‑root users → reduce risk.

  - Health checks → self‑healing containers.

  - Environment variables → flexible config.

  - Secrets management → keep sensitive data out of images.

  - Logging best practices → integrate with orchestration.
