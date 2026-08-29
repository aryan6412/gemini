---
name: postgresql-tuning-prisma
description: |
  PostgreSQL database schema design, index optimization, query tuning, connection pooling (PgBouncer),
  and Prisma ORM best practices.
  
  Covers N+1 query prevention (include vs select), composite indexes (B-Tree, GIN, GiST), full-text search (tsvector),
  raw SQL queries ($queryRaw), transaction isolation levels, partition pruning, and EXPLAIN ANALYZE interpretation.

  Use when modeling schemas with Prisma, diagnosing slow database queries, or optimizing PostgreSQL workloads.
license: MIT
metadata:
  version: v1
---

# PostgreSQL Optimization & Prisma ORM Tuning Guide

Maximize PostgreSQL throughput, reduce query latencies, and eliminate N+1 bottlenecks when working with Prisma ORM.

---

## 1. Eliminate N+1 Queries in Prisma

- Use `select` to only fetch required fields instead of retrieving entire wide table rows.
- Use `include` with indexed foreign keys for batch relation fetching instead of querying inside loops:

```ts
// ❌ Bad: N+1 query loop
const users = await prisma.user.findMany();
for (const user of users) {
  const posts = await prisma.post.findMany({ where: { authorId: user.id } });
}

// ✅ Good: Batched single JOIN query with selected fields
const usersWithPosts = await prisma.user.findMany({
  select: {
    id: true,
    name: true,
    email: true,
    posts: {
      where: { published: true },
      select: { id: true, title: true, createdAt: true },
    },
  },
});
```

---

## 2. Index Optimization Strategies

### Composite Indexes & Left-to-Right Prefix
- Put equality columns first, followed by range / order by columns in the index:

```prisma
model Order {
  id         String   @id @default(cuid())
  userId     String
  status     String
  createdAt  DateTime @default(now())
  totalCents Int

  // Supports queries like: WHERE userId = 'x' AND status = 'COMPLETED' ORDER BY createdAt DESC
  @@index([userId, status, createdAt(sort: Desc)])
}
```

### Specialized Index Types
- **GIN Index**: For JSONB searching, array containment (`ANY`), or full-text search (`tsvector`):
```prisma
model Article {
  id      String   @id @default(cuid())
  title   String
  tags    String[]
  metadata Json

  @@index([tags], type: Gin)
  @@index([metadata], type: Gin)
}
```

---

## 3. Connection Pooling (PgBouncer / Prisma Accelerate)

- When running serverless functions (e.g. AWS Lambda, Vercel, Cloud Run), direct connections exhaust Postgres max connection limits.
- Always use connection pooling with `pgbouncer=true` mode and `connection_limit`:

```env
# Direct connection (used for migrations)
DIRECT_URL="postgresql://user:pass@host:5432/mydb"

# Pooled connection (used by PrismaClient during runtime)
DATABASE_URL="postgresql://user:pass@host:6543/mydb?pgbouncer=true&connection_limit=20"
```

---

## 4. Query Analysis: EXPLAIN (ANALYZE, BUFFERS)

Run raw analysis on slow queries to identify sequential table scans:

```ts
const plan = await prisma.$queryRawUnsafe<any[]>(`
  EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)
  SELECT * FROM "Order"
  WHERE "userId" = 'usr_123' AND "createdAt" >= NOW() - INTERVAL '30 days';
`);
```

Key things to check in the plan:
- **`Seq Scan` vs `Index Scan` / `Bitmap Index Scan`**: If a large table is sequentially scanned, missing index.
- **`Buffers: shared hit vs read`**: Indicates cache hit efficiency.
- **`Actual Rows vs Estimated Rows`**: Significant divergence indicates outdated statistics (`ANALYZE tablename;`).
