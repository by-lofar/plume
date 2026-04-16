# lofar.me

Personal site — portfolio and technology watch feed.

Built with [Astro](https://astro.build), DaisyUI, and Tailwind CSS. Deployed via Coolify.

## Commands

| Command | Action |
| :--- | :--- |
| `npm install` | Install dependencies |
| `npm run dev` | Start the dev server at `localhost:4321` |
| `npm run build` | Build the production site to `./dist/` |
| `npm run preview` | Preview the production build locally |
| `npm run check` | Lint + format via Biome (used in CI) |
| `npx playwright test` | Run e2e tests (requires a prior build) |

## Structure

```
src/
├── components/     # Purely presentational Astro components
│   └── types/      # Shared TypeScript interfaces
├── content/        # Astro content collections (veille, experiences)
├── data/           # Static JSON data (home.json, skills.json)
├── layouts/        # Base layout (SEO, meta tags)
├── pages/          # Routing and data fetching logic
├── styles/         # Global resets and DaisyUI theme overrides
└── config.ts       # Global constants (siteUrl, SEO defaults, etc.)

public/             # Static assets
tests/              # Playwright tests (links, structure, RSS, a11y)
spec/               # Project specifications
```

## Tests

Tests run against the **production build**, never the dev server.

```sh
npm run build
npx playwright test
```

## Specs

Project specifications live in `spec/`. Start with `spec/pages/constitution.md`.
