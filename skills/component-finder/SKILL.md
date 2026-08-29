---
name: component-finder
description: Aggregates, searches, and visually showcases components across 50 specific UI component libraries and registries. Generates an interactive live HTML comparison gallery with one-click code copying, live rendered previews, and framework filters whenever a user asks for any UI component (e.g., buttons, cards, navbars, modals, tables, inputs, loaders).
---

# Component Finder & 50-Library Visual Showcase

This skill acts as an intelligent UI component search engine indexing **50 specific modern component libraries and registries**. Whenever the user requests any component, the agent searches, extracts, and generates an interactive, fully-styled visual gallery comparing designs across these 50 libraries.

---

## 📚 The 50 Indexed Component Libraries & References

### 1. Retro, Pixel & Experimental UI
1. **8BITCN**: Retro 8-bit / pixel art gaming aesthetic components.
2. **CULT UI**: Experimental animated, interactive components.
3. **FANCY COMPONENTS**: Creative micro-interactions, physics, and canvas animations.
4. **HEXTER UI**: Geometric, futuristic, and modern UI elements.

### 2. High-Motion & Animated Libraries
5. **ACETERNITY UI**: Modern 3D effects, spotlights, conic border beams, and canvas glows.
6. **MAGIC UI**: High-converting animated components for landing pages and SaaS.
7. **MOTION PRIMITIVES**: Framer Motion primitives, fluid layout morphing, and transition blocks.
8. **REACT BITS**: Creative animated backgrounds, text effects, and interactive button snippets.
9. **ANIMATE UI**: Fluid CSS animations and keyframe-driven UI elements.
10. **ELDORA UI**: Modern animated templates, cards, and interactive widgets.
11. **LIGHTS WIND**: Tailwind glassmorphism, neon ambient glows, and illuminated borders.
12. **SMOOTH UI**: Soft elevation, fluid transitions, and smooth damping interactions.

### 3. Modern SaaS & Headless Primitives
13. **SHADCN/UI**: Accessible headless UI primitives styled with Tailwind CSS.
14. **BASE CN**: Minimalist, unstyled base primitives for enterprise flexibility.
15. **HERO UI (formerly NextUI)**: High-polish modern React design system with smooth gestures.
16. **JOLLY UI**: Shadcn UI components ported to React Aria for ultimate accessibility.
17. **HEADLESS UI**: Tailwind Labs' unstyled, accessible UI primitives.
18. **KIBO UI**: Accessible component building blocks and modular registry.
19. **21ST DEV**: Modern component registry and community-curated UI patterns.
20. **OPEN SOURCE UI**: Curated open-source web component ecosystem.

### 4. Semantic & Utility Tailwind Systems
21. **DAISY UI**: Semantic Tailwind CSS component classes (themeable with zero JS).
22. **FLYON UI**: Tailwind CSS component library with semantic classes and JS plugins.
23. **FLOWBITE**: Enterprise data-dense components, form inputs, and interactive widgets.
24. **HYPER UI**: Free open-source Tailwind UI components for marketing and e-commerce.
25. **PRELINE UI**: Multi-framework enterprise components (Tailwind + React + Vue + HTML).
26. **MERAKI UI**: Tailwind components for marketing sites, auth pages, and dashboards.
27. **SYNTAX UI**: Copy-paste Tailwind components with modern interactions.
28. **FLOAT UI**: Responsive Tailwind website sections, pricing grids, and heroes.
29. **TAIL GRIDS**: Responsive grids, complex table layouts, and card structures.
30. **TAILARK**: Tailwind marketing blocks and hero layouts.
31. **EASY UI**: High-speed Tailwind developer components and boilerplate blocks.
32. **KIMIA UI**: Lightweight, minimalist Tailwind components.

### 5. Layouts, Data & Dashboard Specialists
33. **TREMOR**: Data visualization, KPI metric cards, badges, and analytics charts.
34. **MVP BLOCK**: High-conversion landing page blocks and call-to-action sections.
35. **UI LAYOUTS**: Curated layout structures, bento grids, and split-screen setups.
36. **SPECTRUM UI**: Sleek dark & light mode aesthetic UI for modern apps.
37. **VELORA UI**: High-end dark mode SaaS interface blocks.
38. **BEAUTIFUL UI**: High-polish aesthetic cards, banners, and buttons.
39. **LEX UI**: Elegant typography, long-form content, and reading interfaces.
40. **REVERSE UI**: Inverted themes, dark-mode-first aesthetic components.
41. **SKYPER UI**: Cloud dashboards and SaaS navigation structures.

### 6. Boutique & Specialized Component Kits
42. **BE UI**: Clean minimalist UI components.
43. **COS UI**: Micro-interaction elements and subtle feedbacks.
44. **WATERMELON UI**: Clean, colorful modern UI kit.
45. **AMICRO**: Compact micro-components and status indicators.
46. **COCONUT UI**: Fresh, colorful, and vibrant modern components.
47. **NEX UI**: Next-gen developer component system.
48. **NINNA UI**: Subtle and sleek modern components.
49. **SERA UI**: Clean dashboard and web application components.
50. **RE UI**: Modular, reusable modern component library.

---

## 🎯 When to Activate
Activate this skill whenever the user:
- Asks for any component: *"I want a button"*, *"Show me card designs"*, *"Find search inputs"*, *"Give me table designs"*.
- References any of the 50 libraries above.
- Wants code snippets and live interactive visual previews comparing multiple library styles.

---

## 🛠️ Execution & Presentation Rules

### 1. Curate from Across the 50 Libraries
Select top variants matching the user's need from distinct categories in the 50-library index (e.g., 1 Animated from Aceternity/Magic UI, 1 SaaS Minimal from Shadcn/Hero UI, 1 Semantic from DaisyUI/Flowbite, 1 Retro/Creative from 8bitcn/Cult UI/Fancy, 1 Metric from Tremor).

### 2. Bulletproof Self-Contained CSS (MANDATORY)
To guarantee 100% reliable rendering in Antigravity webviews without CSP failures:
* **Always embed complete CSS inside `<style>` tags** in the generated HTML file.
* Never rely on external CDN script tags (like `cdn.tailwindcss.com` or font CDNs).

### 3. Numbered Cards & Actions
Each component card must include:
* Numbered badge (`#1`, `#2`, `#3`...)
* Clear library badge (e.g., `ACETERNITY UI`, `SHADCN/UI`, `MAGIC UI`)
* **"⚡ Use in Project"** button (copies selection command)
* **"📋 Copy Code"** button (copies clean source code)

### 4. 1-Click Adoption Workflow
Whenever the user replies with *"Use #1"*, *"Use the Aceternity button"*, or *"Insert #3"*:
1. Detect project tech stack (React, Next.js, Vue, Tailwind, plain HTML/CSS).
2. Create the component file (e.g. `src/components/ui/Button.tsx`).
3. Inject and configure dependencies/styles automatically.
