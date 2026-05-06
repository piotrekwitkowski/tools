# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm install        # Install deps
npm run dev        # Dev server at localhost:4321
npm run build      # Production build → dist/
npm run preview    # Preview production build locally
```

No test runner or linter is configured.

## Architecture

Static site built with **Astro 6** (static output) + **React 19** + **Cloudscape Design System**.

**Page pattern:** Each tool lives at `src/pages/<tool>.astro`, which imports `Layout.astro` and renders a React component with `client:idle` (or `client:only="react"`). React components go in `src/components/`.

**AppLayout:** All pages use `AppLayout.tsx`, which wraps Cloudscape's `AppLayoutToolbar` and injects `ThemeProvider` (context in `src/context/ThemeContext.tsx`) plus a theme-toggle drawer.

**Navigation:** `SideNav.tsx` defines the sidebar links. Some items are dev-only (`import.meta.env.DEV` guard).

**Datasets:** Defined in `src/config/datasets.ts`. Static JSON/TXT files live in `public/static/`. The dynamic route `src/pages/datasets/[...slug].astro` uses `getStaticPaths` to generate pages — new datasets need an entry there too.

**Data update scripts:** `scripts/update-*.mjs` (and one `.py`) fetch external data sources and write to `public/static/`. Corresponding GitHub Actions workflows in `.github/workflows/` run them on schedules and commit changes.

**Fonts:** Cloudscape's bundled `@font-face` rules are stripped by a Vite plugin in `astro.config.mjs`; self-hosted Open Sans woff2 files in `public/static/fonts/` are loaded instead.

## Adding a New Tool

1. Add nav entry in `src/components/SideNav.tsx`.
2. Create page at `src/pages/<tool-name>.astro` wrapping content in `<Layout>`.
3. Use `client:idle` for the React component.

## Adding a New Dataset

1. Add entry to `src/config/datasets.ts`.
2. Add slug to `getStaticPaths` in `src/pages/datasets/[...slug].astro`.
3. Place data file in `public/static/<name>.json`.
4. (Optional) Add update script in `scripts/` and workflow in `.github/workflows/`.

## Deployment

Pushes to `master` trigger `.github/workflows/deploy.yml` which builds and deploys to GitHub Pages. Custom domain: `tools.piotrekwitkowski.com` (via `CNAME` file).
