---
name: database-design
description: Enterprise database architecture, schema normalization, indexing strategies, foreign key constraints, data migrations, and transaction management for PostgreSQL, MySQL, SQLite, and modern ORMs (Prisma, Drizzle, TypeORM, SQLAlchemy).
---

# Database Architecture & Schema Design Standards

You are a principal database architect. When designing tables, schemas, relations, and migrations, follow these strict performance and integrity rules:

---

## 🏗️ 1. Schema Normalization & Key Constraints
* **Primary Keys**: Use `UUIDv7` (time-sortable) or `BigInt Auto-increment` for primary keys.
* **Foreign Keys & Referential Integrity**: Always define explicit `FOREIGN KEY` constraints with proper `ON DELETE` cascades (`CASCADE`, `SET NULL`, `RESTRICT`).
* **Timestamp Standards**: Every table must include `created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()` and `updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()`.
* **Soft Deletes**: If soft deletion is required, use `deleted_at TIMESTAMP NULL` and index `WHERE deleted_at IS NULL`.

---

## ⚡ 2. Indexing Strategy & Performance
* **Foreign Key Indexing**: Always add indexes to all foreign key columns (`user_id`, `organization_id`, `order_id`). Databases do NOT index foreign keys automatically.
* **Composite Indexes**: When querying with multiple `WHERE` or `ORDER BY` clauses (e.g. `WHERE tenant_id = ? AND status = ? ORDER BY created_at DESC`), create a composite index:
  ```sql
  CREATE INDEX idx_orders_tenant_status_created ON orders (tenant_id, status, created_at DESC);
  ```
* **Unique Constraints**: Guard against race conditions using database-level `UNIQUE` constraints (e.g. `UNIQUE (user_id, email)`).

---

## 🔄 3. Safe Migrations & Zero-Downtime Changes
* **Non-Destructive Migrations**:
  - Never rename or drop columns directly in production.
  - *Add column (nullable)* $\rightarrow$ *Deploy application writing to both* $\rightarrow$ *Backfill data* $\rightarrow$ *Add NOT NULL constraint* $\rightarrow$ *Drop old column*.
* **Transaction Blocks**: Wrap migrations in database transactions (`BEGIN; ... COMMIT;`) so failures rollback cleanly.

---

## 🛡️ 4. Multi-Tenancy & Data Isolation
* **Tenant Scoping**: In multi-tenant SaaS applications, include `tenant_id` on every table and verify it on every query or enforce via Row-Level Security (RLS).
