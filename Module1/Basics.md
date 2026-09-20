# 📘 Module 1: Dockerfile Basics

## 🔎 Key Concepts

| Instruction | Purpose |
|-------------|---------|
| `FROM` | Base image (e.g., `python:3.11-slim`) |
| `WORKDIR` | Working directory inside the container |
| `COPY` | Copy files from host into container |
| `RUN` | Execute commands during build (e.g., install dependencies) |
| `CMD` | Default command when container starts |

---

## 🐍 Example: Python Hello World

### App code (`app.py`)

```python
print("Hello from Docker!")
```
---
## Dockerfile
```
# 1. Start from a base image
FROM python:3.11-slim

# 2. Set working directory
WORKDIR /app

# 3. Copy code into container
COPY app.py .

# 4. Define default command
CMD ["python", "app.py"]   
```

## Build & Run
```
# Build image
docker build -t hello-docker .

# Run container
docker run --rm hello-docker   
```
### Expected output
```
Hello from Docker!   
```
