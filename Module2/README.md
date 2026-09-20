# Module 2: Layering, Caching & Best Practices

### 🔎 Key Concepts

* Each `RUN`, `COPY`, and `ADD` instruction creates a **layer**.
* Docker caches layers, so **smart ordering of instructions** can make builds faster.
* A `.dockerignore` file prevents unnecessary files from being included in the build context, helping keep images smaller.
* Always pin base images (for example, `node:20-alpine` or `python:3.11-slim`) for more **reproducible builds**.
