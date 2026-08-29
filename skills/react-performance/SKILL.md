---
name: react-performance
description: |
  Comprehensive performance profiling, render optimization, and memory leak prevention for React 18/19.
  Covers React Compiler, memoization (useMemo, useCallback, React.memo), virtualized lists (TanStack Virtual),
  code splitting (lazy, dynamic imports), Web Vitals (INP, LCP, CLS), and state colocation.

  Use when diagnosing slow React rendering, optimizing UI frame rates, reducing bundle sizes, or eliminating re-renders.
license: MIT
metadata:
  version: v1
---

# React Performance & Render Optimization Guide

Build buttery smooth 60/120 FPS React web applications by eliminating wasted re-renders, minimizing bundle footprints, and keeping the main thread responsive.

---

## 1. Eliminate Unnecessary Re-renders

### Colocate State
- Move state down to where it is needed instead of lifting everything into top-level providers.
- When state changes, only the sub-branch re-renders.

```tsx
// ❌ Bad: Typing in the input re-renders the expensive graph component
export function Dashboard() {
  const [query, setQuery] = useState('');
  return (
    <div>
      <input value={query} onChange={e => setQuery(e.target.value)} />
      <ExpensiveGraph />
    </div>
  );
}

// ✅ Good: Isolate high-frequency state into its own component
export function SearchBar() {
  const [query, setQuery] = useState('');
  return <input value={query} onChange={e => setQuery(e.target.value)} />;
}
```

### Strategic Memoization (`useMemo`, `useCallback`, `React.memo`)
- Use `useCallback` when passing callbacks to memoized children or hook dependency arrays.
- Use `useMemo` for computationally expensive transforms or when reference equality is required for object props.
- Do NOT blindly wrap trivial primitives in `useMemo`.

---

## 2. Virtualization for Large Datasets

- For lists, tables, or grids with $>100$ items, use virtual windowing (`@tanstack/react-virtual` or `react-window`) to only render visible DOM nodes.

```tsx
import { useVirtualizer } from '@tanstack/react-virtual';

export function VirtualList({ items }: { items: string[] }) {
  const parentRef = useRef<HTMLDivElement>(null);
  const virtualizer = useVirtualizer({
    count: items.length,
    getScrollElement: () => parentRef.current,
    estimateSize: () => 40,
    overscan: 5,
  });

  return (
    <div ref={parentRef} className="h-96 overflow-auto">
      <div style={{ height: `${virtualizer.getTotalSize()}px`, position: 'relative' }}>
        {virtualizer.getVirtualItems().map(virtualRow => (
          <div
            key={virtualRow.index}
            style={{
              position: 'absolute',
              top: 0,
              left: 0,
              width: '100%',
              height: `${virtualRow.size}px`,
              transform: `translateY(${virtualRow.start}px)`,
            }}
          >
            {items[virtualRow.index]}
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## 3. Code Splitting & Dynamic Imports

- Split heavy libraries (e.g. chart engines, rich-text editors, 3D canvases) using `React.lazy()` or Next.js `dynamic()`:

```tsx
import dynamic from 'next/dynamic';

const ChartEngine = dynamic(() => import('@/components/HeavyChart'), {
  loading: () => <ChartSkeleton />,
  ssr: false,
});
```

---

## 4. Concurrent Features & Transitions

- Wrap non-urgent updates (filtering, search results, tab switching) in `useTransition` to prevent blocking typing or button clicks:

```tsx
const [isPending, startTransition] = useTransition();

function handleFilterChange(filterValue: string) {
  setImmediateInput(filterValue);
  startTransition(() => {
    setDeferredFilter(filterValue);
  });
}
```

---

## 5. Web Vitals Checklist (INP, LCP, CLS)

- **INP (Interaction to Next Paint)**: Keep JS execution blocks $<50$ms; use `requestIdleCallback` or yield to the main thread for long tasks.
- **LCP (Largest Contentful Paint)**: Prioritize hero images using `fetchpriority="high"`, preconnect fonts, avoid blocking CSS.
- **CLS (Cumulative Layout Shift)**: Explicitly declare `width` & `height` or `aspect-ratio` on all images, videos, and dynamic ads.
