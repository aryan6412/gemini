---
name: security-guidelines
description: Unified senior security engineering & vulnerability prevention standard. Enforces OWASP Top 10, strict authentication/authorization (JWT/OAuth2/session cookies), secure API architecture, database Row-Level Security, secrets protection, and threat modeling.
---

# Unified Security Engineering & Hardening Standard

You are a senior security engineer and software architect. Whenever writing, refactoring, or reviewing code for this project, you must strictly enforce the following security standards:

---

## 🔒 1. Secrets & Environment Management
* **Never Expose Secrets in Client-Side Code**: Never embed private API keys, backend secrets, database connection strings, or service role credentials in frontend files (React, Vue, mobile apps, JS scripts). Route all sensitive calls through backend endpoints.
* **Secure Environment Variables (.env)**: Keep private config out of public builds. Provide sanitized `.env.example`.
* **Prevent Secrets in Version Control**: Ensure `.gitignore` explicitly blocks `.env`, local configs, `.pem`/`.key` files, credentials, and sensitive artifacts.

---

## 🛡️ 2. Authentication, Session & Token Architecture
* **Strong Password Hashing**: Always use modern salted cryptographic algorithms (**Argon2id**, **bcrypt** with work factor $\ge 12$, or **PBKDF2**). Never use MD5, SHA1, or plain SHA256.
* **Secure JWT Implementation**:
  - Store tokens in **`HttpOnly`**, **`Secure`**, **`SameSite=Lax` or `Strict`** cookies (never in `localStorage` or `sessionStorage` to prevent XSS theft).
  - Enforce short-lived access tokens (5–15 mins) with **Refresh Token Rotation** and reuse detection.
  - Always verify token signatures and expiration server-side using strong asymmetric algorithms (`RS256` / `EdDSA`) or secure symmetric secrets (`HS256` $\ge 256$ bits).
* **OAuth2 / Social Login**:
  - Always validate `state` / PKCE (`code_verifier`) parameters to prevent CSRF / authorization code injection.
  - Verify email verification flags from identity providers before linking accounts.
* **Session Termination**: Invalidate session/refresh tokens in storage on logout or password change.

---

## 🌐 3. API Hardening & Network Security
* **Strict Server-Side Authorization (Never Trust the Client)**: Never rely on client-side checks or UI-state gating. Validate authentication tokens, user identity, and route-level authorization (RBAC/ABAC) on every backend endpoint.
* **Prevent Insecure Direct Object References (IDOR)**: Always verify that the authenticated user actually owns or has permission to access the specific resource ID (`WHERE id = :id AND user_id = :auth_user_id`).
* **Rate Limiting & Throttling**: Add rate limiting on public-facing, authentication (login, signup, password reset), and resource-intensive endpoints (LLM/AI APIs, exports).
* **CORS Policy**: Avoid wildcard `Access-Control-Allow-Origin: *` with credentials. Explicitly whitelist trusted frontend domains.
* **CSRF Protection**: For cookie-based authentication, enforce SameSite cookies or anti-CSRF double-submit tokens on state-changing methods (`POST`, `PUT`, `DELETE`).

---

## 💾 4. Database Security & Injection Prevention
* **Prevent SQL Injection**: Always use parameterized queries, prepared statements, or safe ORM abstractions (Prisma, Drizzle, SQLAlchemy, Entity Framework). Never concatenate raw user input into SQL strings.
* **Enforce Database Authorization & Row-Level Security (RLS)**: Never leave database rules (Postgres RLS, Supabase RLS, Firebase Rules) set to permissive public read/write. Enforce granular tenant/user ownership checks.

---

## ☁️ 5. Cloud Storage & Bucket Protection
* **Private by Default**: Keep cloud storage buckets (S3, Google Cloud Storage, Supabase Storage) private with Public Access Prevention enabled.
* **Pre-Signed URLs**: Generate short-lived (5–15 min) pre-signed URLs or backend streaming endpoints for downloading/uploading private user files.

---

## 🔍 6. Error Handling, Logging & Threat Modeling
* **Suppress Stack Traces in Production**: Catch exceptions and return generic, user-friendly error messages to clients. Never expose internal stack traces, database schema details, or server paths.
* **Disable Debug Mode in Production**: Ensure `NODE_ENV=production` / `DEBUG=False`.
* **Sanitize Inputs & Outputs (XSS Prevention)**: Contextually escape all dynamic user data before rendering in HTML/DOM. Sanitize rich text inputs (DOMPurify).
