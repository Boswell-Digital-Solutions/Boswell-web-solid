# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository is the public Boswell Digital Solutions (BDS) business website — a Service-Disabled Veteran-Owned Small Business (SDVOSB) trust surface covering product status, boundaries, and a bounded shop. It is built with SolidJS + Vite, deployed to Render as a Node web service, and includes a lightweight Node backend for health checks and a contact form. Public claims on this site must stay conservative and factual (e.g. VibeForge 1.0 is documented as unfinished, pending a Forge:SMITH refactor — do not overstate product maturity here).

## Common Commands

- `pnpm install` — install dependencies (pnpm 9+, Node 18+/22 preferred)
- `pnpm dev` — run the Vite dev server (port 3000)
- `pnpm build` — production build (`NODE_ENV=production vite build`, verified by a postbuild check for `dist/index.html`)
- `pnpm preview` — preview the built `dist/` output
- `pnpm start` — start the production server (`server/index.cjs`, serves `dist/` + API + `/healthz`)
- `pnpm run lint` — ESLint over `dev/**/*.{js,ts,tsx,jsx}`
- `pnpm run format` — Prettier write
- `bun run tools/qc/stateforge.ts` (via `pnpm run qc:stateforge`) — StateForge QC check

## Architecture

- `dev/` — SolidJS frontend source: `components/` (shared UI), `config/` (meta + shop config), `pages/` (route components), `styles/`, `App.tsx` (router), `index.tsx` (entry)
- `server/index.cjs` — Node web service handling `/healthz`, `POST /api/contact` (validated, honeypotted, rate-limited, appends to `data/contact-submissions.jsonl`), and static serving
- `public/` — static assets
- `docs/` — specs and reports, notably `docs/specs/WEBSITE_REFACTOR_FORGE_ALIGNED.md` and `docs/specs/PUBLIC_PRODUCT_EXPOSURE_RULES.md`
- `render.yaml` — Render deployment config (build: `pnpm install && pnpm build`; start: `node server/index.cjs`; health check: `GET /healthz`)
- Shop listings live in `dev/config/shop.ts`; each product page states what it is, who it's for, what's included/excluded, license/refund terms, and a purchase CTA (placeholder when checkout isn't wired)

## Notes

- Required public routes are enumerated in `README.md` (`/`, `/products`, `/products/vibeforge`, `/shop`, `/shop/[slug]`, `/forge/charter`, `/about`, `/contact`, `/terms`, `/privacy`, `/support`) — keep these in sync with any routing changes.
- `docs/specs/PUBLIC_PRODUCT_EXPOSURE_RULES.md` governs what product claims are allowed to say publicly; check it before editing product-facing copy.
