# Repository Guidelines

10x Astro Starter: Astro 7 SSR + React 19 islands, Tailwind 4, Supabase auth, shadcn/ui, deployed to Cloudflare Workers. Full stack conventions and course-chain process rules live in @CLAUDE.md — this file is a tool-agnostic quick reference.

## Hard rules

- `@.github/workflows/ci.yml` triggers only on branch `master`, but this repo's actual branch is `main` — pushing or opening a PR against `main` does **not** run CI. Confirm the target branch before trusting CI to catch a regression.
- Every file under `src/pages/api/` must export `const prerender = false` (global `output: "server"` in `@astro.config.mjs`) and use uppercase `GET`/`POST` exports validated with zod.
- Use `cn()` from `@/lib/utils` for conditional/merged Tailwind classes; never concatenate class strings manually.
- Enable RLS on every new Supabase table with granular per-operation, per-role policies.
- No Next.js directives (`"use client"`, etc.) in React components.
- Skills governed by `@CLAUDE.md` must never write to `context/archive/` — that tree is immutable.

## Project structure

- `src/pages/` — Astro pages, `src/pages/api/` — API endpoints, `src/pages/auth/` — auth pages.
- `src/components/` — Astro + React; shadcn/ui lives in `src/components/ui/` ("new-york" variant).
- `src/lib/` — helpers/services (e.g. `@src/lib/supabase.ts`); `src/middleware.ts` resolves the session; `src/types.ts` holds shared entities/DTOs.
- Path alias `@/*` → `./src/*`. Migrations: `supabase/migrations/YYYYMMDDHHmmss_description.sql`.

## Commands

- `npm run dev` / `build` / `preview` — Astro on the Cloudflare workerd runtime.
- `npm run lint` / `lint:fix` — type-checked ESLint; `npm run format` — Prettier.
- `npm run test` / `test:watch` — Vitest; `npm run smoke` — auth-flow smoke test against `BASE_URL` (`@scripts/smoke.mjs`), run after dependency upgrades.

## Coding style

- Astro for static content/layout; React only where interactivity is needed. Extract React hooks to `src/components/hooks/`. Add shadcn/ui components with `npx shadcn@latest add [name]`.

## Testing

- Vitest, colocated as `*.test.ts` next to source — see `@src/lib/utils.test.ts`. `jsdom` env configured in `@vitest.config.ts`.

## Commit & CI

- Commit history so far uses short single-word stage labels (`bootstrapper`, `tech`, `prd`) — no enforced message convention yet.
- Pre-commit (husky + lint-staged): `eslint --fix` on `*.{ts,tsx,astro}`, `prettier --write` on `*.{json,css,md}`.
- CI runs `npm audit` (high+), lint, type-check, and build, plus a separate job that runs the smoke test against a local Supabase and the production preview.

## Environment

- `SUPABASE_URL` / `SUPABASE_KEY` are server-only secrets (`astro:env/server`) — copy `@.env.example` to `.env` (Node) or `.dev.vars` (Cloudflare local dev, gitignored).
- Local Supabase: `npx supabase start` (requires Docker).
