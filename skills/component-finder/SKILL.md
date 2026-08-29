---
name: component-finder
description: Aggregates, searches, and visually showcases components across 50+ UI component libraries and local design systems. Generates an interactive live HTML comparison gallery with one-click code copying, live rendered previews, and framework filters whenever a user asks for any UI component (e.g., buttons, cards, navbars, modals, tables, inputs, loaders).
---

# Component Finder & Multi-Library Visual Aggregator

This skill enables the agent to act as a universal UI component search engine across 50+ modern component libraries (Shadcn, DaisyUI, Aceternity, Magic UI, Flowbite, NextUI/HeroUI, Mantine, Chakra, AntD, MUI, Radix, Tailwind UI, Tremor, HyperUI, Park UI, Ark UI, Preline, etc.) as well as local component repositories.

---

## 🎯 When to Activate
Activate this skill whenever the user:
- Asks for any UI component: *"I want a button"*, *"Show me card designs"*, *"Find modal components"*, *"Give me table designs"*.
- Wants to compare component variations or styles across multiple UI libraries.
- Wants code snippets and live interactive visual previews for a specific element.

---

## 🛠️ Step-by-Step Execution Workflow

### 1. Identify Component & Category
Determine the target component requested by the user:
- **Buttons / Actions**: CTA, Gradient, Shimmer, Border-Beam, Magnetic, Glass, Neo-brutalist, Floating, Icon, Outline.
- **Cards / Containers**: KPI cards, pricing tables, product cards, testimonial cards, bento grids, glassmorphism containers.
- **Navigation**: Headers, sticky navbars, mobile drawers, breadcrumbs, tab bars, pagination.
- **Feedback & Loaders**: Skeleton screens, spinners, shimmer placeholders, toasts, alert banners.
- **Inputs & Forms**: Floating label inputs, OTP inputs, password reveal fields, search bars with command palette.
- **Data Display**: Tables, data grids, badges, timelines, accordions, stats.

### 2. Search & Aggregate Variations Across 50+ Libraries
Select top variants from major design ecosystems:
1. **Shadcn UI / Radix**: Clean, accessible, Tailwind-based, headless primitives.
2. **Aceternity UI / Magic UI**: Modern animated, canvas effects, gradient borders, spotlights, border beams.
3. **DaisyUI / Tailwind UI**: Semantic utility classes, fast prototyping, theme-ready.
4. **Flowbite / Preline**: Enterprise data-dense components, interactive widgets.
5. **NextUI (HeroUI) / Mantine / Chakra**: Polished modern SaaS aesthetics, smooth hover/focus transitions.
6. **Local Component Folders** *(if available)*: Scan project workspace (`components/`, `lib/`, `src/`) for existing matching items.

### 3. Generate Interactive Live HTML Showcase
Always generate a self-contained interactive Generative UI artifact in the current artifact directory:
* **File Path**: `<artifact_dir>/component_gallery.html`
* **Features Required in the HTML Showcase**:
  - **Numbered Components (#1, #2, #3...)**: Clear numbered badge on each card so the user can easily select one in chat.
  - **Live Visual Previews**: Render each component variant cleanly with interactive hover/active states.
  - **Filter & Search Bar**: Quick filter by style (Modern, Minimal, Animated, Enterprise, Brutalist, Glassmorphism).
  - **"⚡ Use This in Project" Button**: Clicking copies a ready command (`Use Component #1 in my project`) and highlights the card.
  - **"📋 Copy Code" Button**: Direct raw code copying.
  - **Source Library Badges**: Clear tags indicating which library the design originates from.

### 4. Bulletproof Styling Rule (Self-Contained Embedded CSS)
To ensure 100% reliable, zero-latency, and gorgeous visual rendering in Antigravity webviews (without relying on external CDNs that might be blocked by CSP):
* **Always embed complete CSS directly inside `<style>` tags** in the HTML file (including reset, flex/grid layouts, keyframe animations, dark themes, and hover transitions).
* Never rely solely on external script tags for layout rendering.

### 5. Automatic Component Adoption Workflow ("Use This" Execution)
Whenever the user replies with *"Use #1"*, *"Use this component"*, *"Insert the Aceternity button"*, or triggers the *"Use This in Project"* action:
1. **Analyze Project Tech Stack**: Detect whether the current workspace is using React/Next.js, Vue, Svelte, Tailwind, or plain HTML/CSS.
2. **Create the Component File**: Write the modular component into the project's UI folder (e.g., `src/components/ui/Button.tsx`, `components/Button.jsx`, or project-specific directory).
3. **Configure Dependencies & Styles**: Ensure all necessary Tailwind classes, CSS keyframes, or icon packages (like `lucide-react`) are properly imported.
4. **Insert into Target View**: If the user specified a location (e.g., "Put it in the Hero section"), immediately integrate the `<Button />` into that file.
5. **Report & Verify**: Show the exact file created and a quick import snippet.
