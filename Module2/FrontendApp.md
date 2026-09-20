## Erontend apps — 
this is where Dockerfiles look a bit different because you’re not running a backend service, you’re building static assets and serving them with a web server like Nginx.

### 🎨 Example: React App with Nginx
App structure:
```
my-react-app/
  ├── public/
  ├── src/
  ├── package.json
  └── Dockerfile
```
Dockerfile
```dockerfle
# Stage 1: Build React app
FROM node:20-alpine AS builder

# Set working directory
WORKDIR /app

# Copy dependency files first
COPY package*.json ./

# Install dependencies
RUN npm ci

# Copy rest of the source code
COPY . .

# Build production-ready static files
RUN npm run build

# Stage 2: Serve with Nginx
FROM nginx:1.27-alpine

# Copy built files from builder stage
COPY --from=builder /app/build /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Nginx runs automatically with its default CMD

```
### Explanation
- Stage 1 (builder):

  - FROM node:20-alpine AS builder → lightweight Node.js image to build React.

  - WORKDIR /app → working directory.

  - COPY package*.json ./ → copy dependency files first for caching.

  - RUN npm ci → install dependencies cleanly.

  - COPY . . → copy source code.

  - RUN npm run build → produces optimized static files in /app/build.

- Stage 2 (runtime):

  - FROM nginx:1.27-alpine → lightweight Nginx image.

  - COPY --from=builder /app/build /usr/share/nginx/html → copy built static files into Nginx’s default web root.

  - EXPOSE 80 → container listens on port 80.

  - No CMD needed → Nginx image already defines how to run.

### Build & Run
```
docker build -t react-nginx .
docker run -p 8080:80 react-nginx
```
## 🧩 Key Nuances
- `Frontend vs Backend`: Backend containers run code (Node, Python, Java). Frontend containers serve static files.

- `Multi‑stage build`: First stage compiles assets, second stage serves them.

- `Nginx`: Production‑grade web server, faster and more secure than running npm start in production.
