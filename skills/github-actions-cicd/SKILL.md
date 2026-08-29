---
name: github-actions-cicd
description: Production GitHub Actions CI/CD workflows, automated testing, linting, type-checking, Docker image building with layer caching, security vulnerability scanning, and multi-environment deployment pipelines.
---

# GitHub Actions CI/CD Production Engineering

You are a DevOps & Platform Engineer. When creating or optimizing GitHub Actions workflows, enforce these production patterns:

---

## ⚡ 1. Production CI Pipeline (.github/workflows/ci.yml)

```yaml
name: Continuous Integration

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  validate:
    name: Lint, Typecheck & Test
    runs-on: ubuntu-latest
    timeout-minutes: 15

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - name: Install Dependencies
        run: npm ci

      - name: Run Linter
        run: npm run lint

      - name: Run Type Check
        run: npm run typecheck

      - name: Run Unit & Integration Tests
        run: npm run test:coverage
        env:
          CI: true

      - name: Run Security Audit
        run: npm audit --audit-level=high
```

---

## 🐳 2. Docker Build & Push with Layer Cache

```yaml
  build-image:
    name: Build & Push Docker Container
    needs: validate
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Log in to Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and Push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ghcr.io/${{ github.repository }}:${{ github.sha }},ghcr.io/${{ github.repository }}:latest
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

---

## 🔒 3. Workflow Security Rules
* **Least Privilege**: Always declare explicit `permissions:` block at top level or job level.
* **Secrets Security**: Never echo or print secrets in run commands.
* **Pin Action Versions**: Reference major versions (`@v4`, `@v3`) or commit SHAs.
* **Concurrency Cancellation**: Always use `concurrency: cancel-in-progress: true` to prevent wasting runner minutes on stale PR commits.
