---
name: docker-compose-dev
description: |
  Patterns and configurations for reproducible local multi-container development environments using Docker & Docker Compose.
  Covers multi-stage Dockerfiles (build cache, non-root users), hot-reloading volume mounts, local services (Postgres, Redis, Mailpit, MinIO),
  health checks (depends_on condition: service_healthy), network isolation, and secret management (.env).

  Use when creating, optimizing, or debugging Dockerfiles, docker-compose.yml, and containerized dev environments.
license: MIT
metadata:
  version: v1
---

# Docker & Docker Compose Local Development Standards

Set up clean, isolated, fast-booting local development environments with instant hot-reloading and production-like backing services.

---

## 1. Multi-Stage Dockerfile (Node / Next.js / Python)

Separate development and production targets with dependency layer caching and non-root execution:

```dockerfile
# syntax=docker/dockerfile:1.4
FROM node:20-alpine AS base
WORKDIR /app
RUN apk add --no-cache libc6-compat

FROM base AS deps
COPY package.json package-lock.json* ./
RUN npm ci

FROM base AS dev
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
ENV NODE_ENV=development
EXPOSE 3000
CMD ["npm", "run", "dev"]
```

---

## 2. Docker Compose with Health Checks & Service Dependencies

Always configure proper `healthcheck` declarations and `depends_on: condition: service_healthy` so backend services don't crash waiting for databases to boot:

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      target: dev
    ports:
      - '3000:3000'
    volumes:
      - .:/app
      - /app/node_modules
      - /app/.next
    environment:
      - DATABASE_URL=postgresql://dev:devpass@postgres:5432/devdb?schema=public
      - REDIS_URL=redis://redis:6379
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy
    networks:
      - dev-network

  postgres:
    image: postgres:16-alpine
    restart: unless-stopped
    ports:
      - '5432:5432'
    environment:
      POSTGRES_USER: dev
      POSTGRES_PASSWORD: devpass
      POSTGRES_DB: devdb
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U dev -d devdb"]
      interval: 5s
      timeout: 5s
      retries: 5
    networks:
      - dev-network

  redis:
    image: redis:7-alpine
    restart: unless-stopped
    ports:
      - '6379:6379'
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 5s
      retries: 5
    networks:
      - dev-network

  mailpit:
    image: axllent/mailpit:latest
    ports:
      - '1025:1025' # SMTP port
      - '8025:8025' # Web UI port
    networks:
      - dev-network

volumes:
  postgres_data:

networks:
  dev-network:
    driver: bridge
```

---

## 3. Hot-Reload & File Watching Tips

- **Anonymous volume for `node_modules` / `.venv`**: Prevents host machine operating system binaries from clobbering Linux container binaries (`/app/node_modules`).
- **File watching polling**: If hot-reloading fails across Windows / WSL2 host mounts, set `WATCHPACK_POLLING=true` (Next.js/Webpack) or `CHOKIDAR_USEPOLLING=true` (Vite).
