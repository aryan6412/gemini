---
name: ui-ux-pro-max
description: >
  Premium UI/UX design standard for the VELOX ERP project.
  MUST be applied on every UI change — modals, tables, buttons, forms, badges, cards, tabs, alerts.
  Enforces visual excellence, micro-interactions, spacing discipline, color hierarchy, and modern design patterns.
---

# UI/UX PRO MAX — VELOX ERP Design Standard

## 🚨 MANDATORY ACTIVATION
You MUST apply these rules on **every UI change** in the VELOX ERP project — no exceptions.
This includes: modals, forms, buttons, table rows, badges, cards, alerts, navigation, tooltips, and any inline HTML.

---

## 1. COLOR & BADGE SYSTEM
Use Bootstrap 5 semantic-subtle variants for all status badges. Never use flat `bg-primary` / `bg-success` for badges in tables — always use the subtle border variant:

```html
<!-- ✅ Correct badge style -->
<span class="badge bg-success-subtle text-success border border-success border-opacity-25 fw-semibold rounded-pill px-2">
    <i class="bi bi-check-circle-fill me-1"></i>Paid
</span>

<!-- ❌ Wrong — too flat -->
<span class="badge bg-success">Paid</span>
```

**Status Color Map:**
| Status | bg | text | border |
|---|---|---|---|
| Paid / Active / Approved | `bg-success-subtle` | `text-success` | `border-success` |
| Unpaid / Pending / Warning | `bg-warning-subtle` | `text-warning-emphasis` | `border-warning` |
| Rejected / Error / Overdue | `bg-danger-subtle` | `text-danger` | `border-danger` |
| Draft / Info / In-Progress | `bg-info-subtle` | `text-info` | `border-info` |
| Neutral / None | `bg-light` | `text-secondary` | `border-secondary` |

---

## 2. BUTTON STANDARDS
Every button must have:
- `fw-semibold` or `fw-bold` weight
- Appropriate icon from Bootstrap Icons (`bi bi-*`)
- `btn-sm` for table rows and modals, `btn` for primary page CTAs
- Hover/focus states are automatic via Bootstrap — do NOT write custom `:hover` unless adding transition

```html
<!-- Primary action -->
<button class="btn btn-primary fw-bold px-4 shadow-sm">
    <i class="bi bi-plus-circle me-2"></i>Create Invoice
</button>

<!-- Destructive -->
<button class="btn btn-danger btn-sm fw-semibold px-3">
    <i class="bi bi-trash me-1"></i>Delete
</button>

<!-- Secondary/ghost -->
<button class="btn btn-outline-secondary btn-sm fw-semibold">Cancel</button>
```

---

## 3. MODAL STANDARDS
Every modal must follow this structure:

```html
<div class="modal fade" id="myModal" tabindex="-1">
    <div class="modal-dialog modal-dialog-centered modal-lg">
        <div class="modal-content border-0 shadow-lg rounded-4">

            <!-- Header: colored left accent or icon -->
            <div class="modal-header bg-white border-bottom py-3 px-4">
                <h5 class="modal-title fw-bold text-dark d-flex align-items-center gap-2">
                    <span class="bg-primary-subtle text-primary rounded-3 p-1.5 d-inline-flex">
                        <i class="bi bi-receipt fs-5"></i>
                    </span>
                    Modal Title
                </h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
            </div>

            <!-- Body: sectioned with subtle headers -->
            <div class="modal-body px-4 py-3">
                <!-- Section header -->
                <h6 class="fw-bold small text-uppercase text-muted tracking-wider mb-2">
                    <i class="bi bi-info-circle me-1"></i>Section Name
                </h6>
                <!-- Form or content -->
            </div>

            <!-- Footer: right-aligned with cancel on left -->
            <div class="modal-footer border-top px-4 py-3 d-flex justify-content-between align-items-center">
                <button type="button" class="btn btn-outline-secondary btn-sm fw-semibold" data-bs-dismiss="modal">
                    Cancel
                </button>
                <button type="submit" class="btn btn-primary fw-bold px-4">
                    <i class="bi bi-check-lg me-1"></i>Save
                </button>
            </div>

        </div>
    </div>
</div>
```

**Modal Rules:**
- Always `modal-dialog-centered`
- Always `border-0 shadow-lg rounded-4` on `.modal-content`
- Modal header icon uses colored subtle background circle
- Modal body sections use small uppercase muted labels
- Footer ALWAYS has Cancel on LEFT, primary action on RIGHT

---

## 4. FORM FIELD STANDARDS
```html
<div class="mb-3">
    <label class="form-label fw-semibold small text-dark mb-1">Field Label *</label>
    <input type="text" class="form-control form-control-sm shadow-none" placeholder="Placeholder text">
    <div class="form-text text-muted small">Helper hint text if needed.</div>
</div>
```

**Rules:**
- Always `form-control-sm` inside modals
- Always `shadow-none` on inputs to avoid ugly blue glow
- Label: `fw-semibold small text-dark`
- Required fields get `*` suffix in label, no separate "(required)" text

---

## 5. TABLE STANDARDS
```html
<table class="table table-hover align-middle mb-0" style="width:100%">
    <thead class="table-light">
        <tr>
            <th class="fw-bold small text-uppercase text-muted ps-3">Column</th>
        </tr>
    </thead>
    <tbody>
        <tr class="border-bottom">
            <td class="ps-3 py-3">Content</td>
        </tr>
    </tbody>
</table>
```

**Rules:**
- `align-middle` always on `<table>`
- `table-hover` for interactivity
- `thead` uses `table-light` with `small text-uppercase text-muted fw-bold`
- Row actions: grouped in `d-flex gap-1 justify-content-end`
- IDs shown as `font-monospace fw-bold`
- Amounts shown as `font-monospace text-success fw-bold`

---

## 6. CARD / KPI STANDARDS
```html
<div class="card border-0 shadow-sm rounded-3 h-100">
    <div class="card-body p-3 d-flex align-items-center gap-3">
        <div class="bg-primary-subtle text-primary rounded-3 p-2 d-flex align-items-center justify-content-center"
             style="width:48px; height:48px; flex-shrink:0;">
            <i class="bi bi-receipt fs-4"></i>
        </div>
        <div>
            <div class="fw-bold fs-5 text-dark lh-1">₹1,23,456</div>
            <div class="small text-muted mt-1">Total Invoiced</div>
        </div>
    </div>
</div>
```

---

## 7. ALERT / NOTICE PANELS
```html
<div class="alert border-0 shadow-sm rounded-3 p-0 overflow-hidden mb-4"
     style="background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%);">
    <div class="d-flex align-items-center px-4 py-3">
        <div class="bg-info text-white rounded-3 p-2 me-3 d-flex align-items-center justify-content-center"
             style="width:42px; height:42px; flex-shrink:0;">
            <i class="bi bi-info-circle-fill fs-5"></i>
        </div>
        <div>
            <div class="fw-bold text-dark">Alert Heading</div>
            <div class="small text-muted">Supporting description text.</div>
        </div>
    </div>
</div>
```

---

## 8. SPACING & LAYOUT RULES
- Section gaps: `mb-4` between major sections, `mb-3` between form groups, `mb-2` between label and input
- Card grid: always `row g-3` for KPI cards, `row g-2` for dense grids
- Padding in cards/modals: `p-3` standard, `p-4` for premium modals
- Never use inline `style="margin..."` — always use Bootstrap spacing utilities
- Responsive: every row must work on `col-12` on mobile → use `col-md-*` for desktop splits

---

## 9. MICRO-INTERACTION RULES
- All action buttons in tables must have a `disabled` + spinner state while AJAX is running:
```javascript
btn.prop('disabled', true).html('<span class="spinner-border spinner-border-sm me-1"></span> Loading...');
```
- Success actions reload after 800–1000ms delay with `showToast()` first
- Destructive actions require `confirm()` before proceeding
- Form resets after successful modal submission

---

## 10. FORBIDDEN PATTERNS (never use these)
- ❌ `bg-success` / `bg-danger` / `bg-warning` as flat badge backgrounds in tables
- ❌ `style="color: red"` or any inline color — use utility classes
- ❌ `<br>` for spacing — use margin/padding utilities
- ❌ Dashboard-style `border-left: 4px solid` card accents
- ❌ `text-gradient` or CSS gradient text fills
- ❌ Grid/mesh backgrounds
- ❌ Glowing colored borders or box-shadows with color tints
- ❌ Large hero text without proper `letter-spacing` / `line-height`
- ❌ Nested cards (cards inside cards inside cards)
- ❌ Using placeholder images — generate real ones with `generate_image`

---

---

## 11. ZERO-RELOAD (AJAX / SPA) ARCHITECTURE STANDARD (MANDATORY)
To ensure desktop-app responsiveness and eliminate layout flicker/scroll loss:

### ⚡ Use No-Reload (AJAX & In-Place DOM Updates) for:
1. **Dropdown Selectors & Filters**: Changing employees, customers, warehouses, date ranges, or payment modes.
2. **Monthly / Weekly Calendars & Roster Grids**: Prev/next month pagination, date picker changes, day status clicks.
3. **Status Updates & Approvals**: Approving leaves, updating ticket statuses, marking attendance, toggling permissions.
4. **Modal Submissions**: Create/edit forms submit via AJAX, close modal, show toast, and dynamically update the target table/card.
5. **DataTables Filters & Search**: Column filtering, search inputs, pagination, and pill buttons.
6. **URL & Bookmark Sync**: Always call `window.history.replaceState(null, '', newUrl)` on major AJAX filter changes so URLs stay bookmarkable without reloading.

### 🔄 Use Full-Page Reload ONLY for:
1. **Authentication Events**: Login, Logout, Session expiration.
2. **Major Top-Level Module Switching**: Navigating between primary routes (e.g. `index.php?route=inventory` ➔ `index.php?route=payroll`).
3. **Hard System Config / Database Restores**.

---

## 12. 16 PROACTIVE UI/UX ENHANCEMENTS

When designing, refactoring, or polishing frontend components and user interfaces, proactively implement and offer the following 16 UI/UX enhancements where relevant:

### 1. Dark Mode Toggle
- Implement persistent theme toggling (Light/Dark/System) using standard CSS variables or Tailwind `dark:` classes with `localStorage` state retention.

### 2. Cookie & Privacy Consent Banner
- Provide a clean, non-intrusive banner for cookie consent and privacy preference management.

### 3. Back-to-Top Button
- Add a smooth-scrolling floating trigger that conditionally appears when scrolling past 300px.

### 4. Responsive Mobile Navigation
- Build accessible hamburger drawers/menus with touch-friendly targets, backdrop blur, and focus trapping.

### 5. Global Keyboard Shortcuts
- Include standard keyboard event listeners (e.g., `Cmd/Ctrl + K` command palette, `Escape` to close modals).

### 6. Interactive Hover & Focus States
- Add clear micro-interactions, cursor feedback, focus rings for accessibility, and smooth transitions on all clickable elements.

### 7. Scroll Progress Bar
- Implement a subtle top progress bar showing reading or scroll completion on long-form content.

### 8. One-Click Copy Buttons
- Include copy-to-clipboard functionality with instant visual feedback (e.g., checkmark icon or tooltip) on code blocks, links, and tokens.

### 9. Skeleton Loaders
- Render pulse/shimmer skeleton placeholders during asynchronous data fetches instead of blank screens or jarring spinners.

### 10. Sticky Navigation Header
- Keep main navigation accessible with fixed/sticky positioning and subtle elevation/blur on scroll.

### 11. Skip-to-Content Link
- Include an accessible `#main-content` skip link at the top of the DOM for screen reader and keyboard navigation.

### 12. OpenGraph & Social Meta Tags
- Generate complete OpenGraph and Twitter card metadata (title, description, dynamic image, URL) for every shareable route.

### 13. Engaging Empty States
- Create informative zero-data illustrations/messages with clear call-to-action buttons (e.g., "Create your first project").

### 14. Expandable FAQ Accordions
- Organize FAQs and dense text into smooth, accessible accordion collapse components (`<details>/<summary>` or accessible ARIA panels).

### 15. Toast Notifications
- Provide non-blocking, auto-dismissing toast alerts for asynchronous operations (success, error, warning, info).

### 16. Password Visibility Toggle
- Add an interactive show/hide toggle inside password input fields to improve user login and registration accuracy.

---

## VELOX ERP BRAND TOKENS
```css
--velox-primary: #0d6efd;          /* Bootstrap primary */
--velox-accent: #0f172a;           /* Deep navy for headings */
--velox-success: #16a34a;
--velox-warning: #d97706;
--velox-danger: #dc2626;
--velox-muted: #64748b;
--velox-surface: #f8fafc;          /* Card / page background */
--velox-border: #e2e8f0;
```

All tables, modals and pages must feel like a **premium Indian B2B ERP** — clean, data-dense, highly legible, with confident typography and calm color usage. Think: Zoho Books quality, not a college project.
