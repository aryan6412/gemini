---
name: tailwind-design-system
description: |
  Enterprise design system architecture and component patterns using Tailwind CSS (v3/v4),
  Tailwind Merge (twMerge), Class Variance Authority (CVA), and Radix UI / Shadcn primitives.
  
  Covers dark mode theming (CSS variables), token management, accessible reusable components,
  responsive layout systems, micro-interactions, and design tokens.
license: MIT
metadata:
  version: v1
---

# Tailwind CSS Enterprise Design System Guide

Construct consistent, accessible, scalable design systems and component libraries using Tailwind CSS and modern composition utilities.

---

## 1. Core Utilities & Helper Setup

Ensure `clsx` and `tailwind-merge` are configured to properly resolve conflicting class overrides:

```ts
// lib/utils.ts
import { clsx, type ClassValue } from 'clsx';
import { twMerge } from 'tailwind-merge';

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

---

## 2. Component Variant Authoring with CVA (Class Variance Authority)

Define type-safe component variants (size, intent, variant, outline) with default fallbacks:

```tsx
// components/ui/button.tsx
import * as React from 'react';
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/lib/utils';

export const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-lg font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        primary: 'bg-indigo-600 text-white hover:bg-indigo-700 focus-visible:ring-indigo-500 shadow-sm',
        secondary: 'bg-zinc-100 text-zinc-900 hover:bg-zinc-200 dark:bg-zinc-800 dark:text-zinc-100 dark:hover:bg-zinc-700',
        destructive: 'bg-rose-600 text-white hover:bg-rose-700 focus-visible:ring-rose-500',
        ghost: 'hover:bg-zinc-100 dark:hover:bg-zinc-800 text-zinc-700 dark:text-zinc-300',
        outline: 'border border-zinc-300 bg-transparent hover:bg-zinc-50 dark:border-zinc-700 dark:hover:bg-zinc-800',
      },
      size: {
        sm: 'h-8 px-3 text-xs gap-1.5',
        md: 'h-10 px-4 text-sm gap-2',
        lg: 'h-12 px-6 text-base gap-2.5',
        icon: 'h-10 w-10 p-0',
      },
    },
    defaultVariants: {
      variant: 'primary',
      size: 'md',
    },
  }
);

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {}

export const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, ...props }, ref) => {
    return (
      <button
        ref={ref}
        className={cn(buttonVariants({ variant, size, className }))}
        {...props}
      />
    );
  }
);
Button.displayName = 'Button';
```

---

## 3. Theming & Semantic CSS Variables

Use HSL or OKLCH CSS variables in `:root` and `.dark` to power your Tailwind config dynamically:

```css
/* globals.css */
@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 240 10% 3.9%;
    --card: 0 0% 100%;
    --card-foreground: 240 10% 3.9%;
    --primary: 240 5.9% 10%;
    --primary-foreground: 0 0% 98%;
    --muted: 240 4.8% 95.9%;
    --muted-foreground: 240 3.8% 46.1%;
    --border: 240 5.9% 90%;
    --radius: 0.5rem;
  }
  .dark {
    --background: 240 10% 3.9%;
    --foreground: 0 0% 98%;
    --card: 240 10% 3.9%;
    --card-foreground: 0 0% 98%;
    --primary: 0 0% 98%;
    --primary-foreground: 240 5.9% 10%;
    --muted: 240 3.7% 15.9%;
    --muted-foreground: 240 5% 64.9%;
    --border: 240 3.7% 15.9%;
  }
}
```

---

## 4. Spacing & Typography Discipline

- **Spacing Multiples**: Stick to regular standard scale increments (`gap-2`, `gap-3`, `gap-4`, `gap-6`, `gap-8`).
- **Surface Elevation**: Layer cards using subtle borders (`border border-zinc-200 dark:border-zinc-800`) rather than harsh drop-shadows.
- **Typography Scale**: Ensure clear hierarchy: `text-xs font-semibold uppercase tracking-wider` for labels, `text-sm text-zinc-500` for subtitles, `text-2xl font-bold` for headings.
