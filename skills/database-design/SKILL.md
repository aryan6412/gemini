---
name: database-design
description: Enterprise database architecture, schema normalization, indexing strategies, foreign key constraints, data migrations, transaction management, connection pooling (PgBouncer), and ORM query optimization (Prisma, Drizzle, SQLAlchemy) for PostgreSQL, MySQL, and SQLite.
---

# Unified Database Architecture & Query Optimization Standards

You are a principal database architect and performance engineer. When designing schemas, writing migrations, modeling relations, or tuning ORM queries (Prisma, Drizzle, SQLAlchemy), enforce these standards:

---

## 🏗️ 1. Schema Normalization & Key Constraints
* **Primary Keys**: Use `UUIDv7` (time-sortable) or `BigInt Auto-increment` for primary keys.
* **Foreign Keys & Referential Integrity**: Always define explicit `FOREIGN KEY` constraints with proper `ON DELETE` cascades (`CASCADE`, `SET NULL`, `RESTRICT`).
* **Timestamp Standards**: Every table must include `created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()` and `updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()`.
* **Soft Deletes**: If soft deletion is required, use `deleted_at TIMESTAMP NULL` and index `WHERE deleted_at IS NULL`.

---

## ⚡ 2. Index Optimization & N+1 Prevention
* **Foreign Key Indexing**: Always add indexes to all foreign key columns (`user_id`, `organization_id`, `order_id`). Databases do NOT index foreign keys automatically.
* **Composite Indexes (Left-to-Right Prefix Rule)**: Put equality columns first, followed by range/sort columns:
  ```sql
  CREATE INDEX idx_orders_tenant_status_created ON orders (tenant_id, status, created_at DESC);
  ```
* **Specialized Index Types (GIN / B-Tree)**:
  - Use **GIN indexes** for JSONB containment, arrays (`tags text[]`), and full-text search (`tsvector`).
  - Use **B-Tree** for standard equality, range (`<`, `>`, `BETWEEN`), and sorting.
* **Eliminate N+1 Queries in ORMs**:
  - Always batch relational lookups (`include` with foreign key indexes) or use explicit joins.
  - Select only needed columns (`select: { id: true, name: true }`) instead of querying wide tables (`SELECT *`).

---

## 🔄 3. Safe Migrations & Zero-Downtime Changes
* **Non-Destructive Migrations**:
  - Never drop or rename columns directly in production.
  - *Add column (nullable)* $\rightarrow$ *Deploy application writing to both* $\rightarrow$ *Backfill data* $\rightarrow$ *Add NOT NULL constraint* $\rightarrow$ *Drop old column*.
* **Transaction Blocks**: Wrap migrations in database transactions (`BEGIN; ... COMMIT;`) so failures rollback cleanly.

---

## 🔌 4. Connection Pooling & Multi-Tenancy
* **Connection Pooling (PgBouncer / Prisma Accelerate)**:
  - When running in serverless / containerized environments, always route runtime traffic through connection pooling with `pgbouncer=true&connection_limit=20` to prevent exhausting connection limits.
  - Keep direct database URLs reserved only for schema migrations.
* **Tenant Isolation**: Include `tenant_id` on every table and verify it on every query or enforce via Row-Level Security (RLS).
