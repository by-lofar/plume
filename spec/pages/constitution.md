# constitution.md

> Non-negotiable principles for this project. Every technical and design decision defers to this document. It supersedes momentary preferences.

---

## 1. Identity and intent

This site is the online identity of a craft developer. It serves two distinct purposes:

- **Portfolio** — demonstrate a way of working, not just a list of things built
- **Veille** — share commented links on dev and AI, without friction

The site must reflect the same values as the code it presents: clarity, restraint, care.

---

## 2. Scope

Seven sections, no more:

- `/` — home / hero, includes contact information and links
- `/a-propos` — short bio
- `/experiences` — professional experience and skills
- `/veille` — link feed with optional comments
- `/projets` — technical portfolio
- `/now` — current focus and activities, in the spirit of [nownownow.com](https://nownownow.com)
- `/mentions-legales` — legal notice (required by French law)

`/a-propos`, `/now`, and `/mentions-legales` are plain Markdown pages — no custom data model, no collection.

Contact is handled on the home page. No dedicated contact section. Any new section must be justified against these seven. Scope does not grow by default.

---

## 3. Design

### Minimalism as constraint, not aesthetic

Every element must earn its place. If removing it does not break meaning, remove it.

### Styling

DaisyUI on top of Tailwind CSS. The active theme is **Flexoki**, applied in monochromatic mode — the accent palette is used sparingly, if at all. Colour conveys structure, not decoration.

Use DaisyUI semantic tokens (`base-100`, `primary`, `neutral`, etc.) rather than raw Tailwind color utilities wherever possible — this keeps theming consistent and changes contained.

Custom CSS is plain CSS. No SCSS, no preprocessor. Custom overrides go in component-scoped `<style>` blocks, not global files.

### No decorative complexity

No gradients, no animations unless they carry meaning, no shadows unless they encode depth. Typography and whitespace do the work.

---

## 4. Technical constraints

### Framework

Astro — static site generation only. No server-side rendering, no edge functions.

### Integrations

- `@astrojs/sitemap` — sitemap generation, required for Playwright link validation in CI
- `@astrojs/mdx` — MDX support for richer content authoring

### JavaScript

The site must be fully functional with JavaScript disabled. JS is permitted only when it provides a genuine enhancement (e.g. theme toggle) and only under these conditions:

- The feature is invisible when JS is disabled — not broken, not degraded, invisible
- The enhancement is impossible to achieve with CSS alone
- It ships with no runtime framework (no React, no Vue islands)
- It is written in **TypeScript** — no plain `.js` files. Type checking acts as a lightweight verification layer on client-side code.

### Dependencies

Every dependency must be justified. Before adding a package, the question is: does the value outweigh the cost in maintenance, bundle size, and conceptual overhead? Prefer Astro built-ins and web platform APIs.

Current allowed dependencies: Astro, Tailwind CSS, DaisyUI, Playwright (dev), @axe-core/playwright (dev).

---

---

## 5. Code architecture

### Separation of concerns

Pages own their data-fetching and transformation logic. Components are purely presentational: they receive typed props, they render markup.

```
src/
├── components/   # .astro templates only — typed props, no logic
│   └── types/    # shared TypeScript types and interfaces
├── content/      # Astro content collections
├── layouts/
├── pages/        # data fetching and transformation live here
└── styles/       # global resets and DaisyUI theme overrides only
```

### Component rules

- No data fetching inside components
- No conditional logic beyond simple prop-driven rendering
- All props are explicitly typed via interfaces defined in `components/types/`

---

## 6. What this project is not

- A blog platform — long-form writing is out of scope
- A dashboard — no dynamic data, no auth, no user state
- A design showcase — the design serves the content, not the reverse