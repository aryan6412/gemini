---
name: nextjs-best-practices
description: |
  Master guidelines, patterns, and optimization rules for Next.js App Router (14/15+).
  Covers Server Components (RSC), Client Components, Server Actions, route handlers,
  caching strategies (fetch cache, tag revalidation, dynamic routes), metadata/SEO,
  and parallel/intercepting routes.
  
  Use when building, architecting, or refactoring Next.js applications with TypeScript.
license: MIT
metadata:
  version: v1
---

# Next.js App Router Best Practices

Apply modern Next.js patterns to build fast, secure, scalable applications.

---

## 1. Component Architecture & Server/Client Boundary

### Server Components by Default (RSC)
- Keep components as **Server Components** by default for direct DB access, zero client bundle overhead, and reduced hydration cost.
- Push `"use client"` as far down the component tree as possible (leaves only).
- Pass Server Components as `children` or JSX slots into Client Components to avoid forcing the whole subtree into the client bundle.

```tsx
// ✅ Correct: Slotting Server Component into Client Component
'use client';
export function ModalShell({ children }: { children: React.ReactNode }) {
  const [isOpen, setIsOpen] = useState(false);
  if (!isOpen) return null;
  return <div className="modal">{children}</div>;
}
```

### Passing Data Across Boundaries
- Only pass **serializable props** from Server to Client Components.
- Never pass functions (except Server Actions) across the boundary.

---

## 2. Data Fetching & Caching Strategies

### Parallel Data Fetching
- Avoid request waterfalls by batching asynchronous calls with `Promise.all`:

```tsx
export default async function DashboardPage() {
  const [user, metrics] = await Promise.all([
    getUserProfile(),
    getAnalyticsMetrics(),
  ]);
  return <DashboardView user={user} metrics={metrics} />;
}
```

### Granular Caching & Tag Revalidation
- Use tagged fetches for on-demand cache invalidation:
```ts
// Fetching with tags
const res = await fetch('https://api.example.com/products', {
  next: { tags: ['products'], revalidate: 3600 },
});

// Revalidating inside a Server Action
'use server';
import { revalidateTag, revalidatePath } from 'next/cache';

export async function addProduct(formData: FormData) {
  await db.product.create({ ... });
  revalidateTag('products');
}
```

---

## 3. Server Actions & Mutations

- Always validate inputs using schema validation (e.g. `zod`).
- Enforce authentication and role checks inside Server Actions.
- Return structured result objects `{ success: boolean, data?: T, error?: string }` instead of throwing raw errors to the client.

```ts
'use server';
import { z } from 'zod';

const schema = z.object({ email: z.string().email() });

export async function subscribeNewsletter(prevState: any, formData: FormData) {
  const parsed = schema.safeParse({ email: formData.get('email') });
  if (!parsed.success) {
    return { success: false, error: 'Invalid email address' };
  }
  await db.subscriber.create({ data: parsed.data });
  return { success: true, error: null };
}
```

---

## 4. Metadata & SEO Optimization

- Implement dynamic metadata with `generateMetadata`:
```tsx
import type { Metadata } from 'next';

export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const item = await getItem(params.id);
  return {
    title: `${item.title} | Brand`,
    description: item.summary,
    openGraph: {
      title: item.title,
      images: [item.heroImageUrl],
    },
  };
}
```

---

## 5. Route Handlers (`app/api/`)

- Use Route Handlers for webhooks, file streaming, external consumer APIs, and third-party integrations.
- Always handle CORS, rate-limiting, and error responses with standard `Response.json()`.
