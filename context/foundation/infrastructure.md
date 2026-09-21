---
project: parish-website
researched_at: 2026-09-21
recommended_platform: Cloudflare Workers
runner_up: Netlify
context_type: mvp
tech_stack:
  language: TypeScript/JavaScript
  framework: Astro 7 (React 19 islands)
  runtime: Cloudflare Workers (workerd) via @astrojs/cloudflare adapter
---

## Recommendation

**Deploy on Cloudflare Workers.**

The project is already scaffolded for it (`@astrojs/cloudflare` adapter, `output: "server"`, `wrangler.jsonc`) — no adapter swap, no rework. It scored 5/5 Pass on the agent-friendly criteria (CLI-first via `wrangler`, fully managed, `llms.txt`-published docs, deterministic `wrangler deploy`/`wrangler rollback`, GA MCP catalog), and its free tier (100,000 requests/day) comfortably covers the parish's expected tens-to-low-hundreds monthly users at **$0/month**, matching the developer's explicit "won't pay for deploy" constraint. No other researched platform beats this without adding either monthly cost (Fly.io, Railway, Render) or a real migration effort (all five alternatives require swapping the Astro adapter or accepting weaker rollback ergonomics).

## Platform Comparison

| Platform | CLI-first | Managed/Serverless | Agent-readable docs | Stable deploy API | MCP/Integration | Total |
|---|---|---|---|---|---|---|
| **Cloudflare Workers** | Pass | Pass | Pass | Pass | Pass | 5 Pass |
| Netlify | Partial | Pass | Pass | Partial | Pass | 3 Pass / 2 Partial |
| Vercel | Pass | Pass | Pass | Partial | Partial | 3 Pass / 2 Partial |
| Render | Pass | Pass | Pass | Pass | Partial | 4 Pass / 1 Partial |
| Fly.io | Pass | Pass | Pass | Partial | Partial | 3 Pass / 2 Partial |
| Railway | Partial | Pass | Pass | Partial | Partial | 2 Pass / 3 Partial |

- **Cloudflare Workers**: `wrangler deploy` / `wrangler rollback <id>` / `wrangler tail` are all GA and fully non-interactive. Docs are published as `llms.txt` and per-product `llms-full.txt`. Free tier: 100k requests/day, far above this project's expected load.
- **Netlify**: GA `@astrojs/netlify` adapter and an official, GA MCP server — the strongest MCP story of the alternatives — but rollback is a dashboard "Publish Deploy" click, not a CLI verb, which fails the "agent cannot click" bar on recovery specifically.
- **Vercel**: Mature CLI including `vercel rollback`, but Hobby-tier rollback only targets the immediately-previous deployment, its MCP server is explicitly Beta, and the Hobby free tier's fair-use terms restrict "non-commercial" use in a way that's ambiguous for a paid client build.
- **Render**: Scored second-highest technically (GA CLI + REST rollback API, EU/Frankfurt region), but the free tier sleeps after 15 minutes idle (30-60s cold start on the next visitor) and an always-on Starter instance costs $7/month — conflicts with the no-pay-for-deploy constraint.
- **Fly.io**: True persistent VMs with native WebSocket support (unnecessary here), but no free allowance since 2024 (~$2-6/month realistic cost), no atomic rollback command, and MCP is explicitly experimental.
- **Railway**: Weakest CLI-rollback story (dashboard-only, like Netlify) plus a $5/month flat minimum and an easy-to-misconfigure "Serverless" sleep feature that must be manually kept off.

### Shortlisted Platforms

#### 1. Cloudflare Workers (Recommended)

Already the scaffolded target — zero migration cost. Full pass on all five agent-friendly criteria. Free at this project's scale indefinitely (100k req/day vs. an expected load of tens-to-hundreds of users/month).

#### 2. Netlify

Best MCP integration of the alternatives (GA, official) and a GA Astro adapter, staying at $0 on the credit-based free plan. Held back only by dashboard-only rollback, and would require adopting `@astrojs/netlify` in place of the already-working `@astrojs/cloudflare` adapter for no functional gain.

#### 3. Vercel

Best rollback CLI among the alternatives, also free at this scale on Hobby. Held back by the Hobby plan's non-commercial fair-use restriction (ambiguous for a client-paid build) and a Beta-labeled MCP server.

## Anti-Bias Cross-Check: Cloudflare Workers

### Devil's Advocate — Weaknesses

1. A community-reported (unverified against Cloudflare's own changelog) interaction between the `nodejs_compat` compatibility flag and Astro's Node-detection can silently misbehave under Workers' newer `process` polyfill — could quietly break code relying on Node APIs (e.g. verification-code hashing).
2. The free tier's 10ms-CPU-per-request limit could throttle the PRD's profanity filter and "urgent flag frequency" check if either grows heavier than expected, with no obvious error surfaced to the user.
3. Debugging inside `workerd` differs from plain Node — an npm package that assumes full Node API support can work locally and fail only in production.
4. No single "everything in one place" vendor: secrets (Wrangler), database/auth (Supabase), and outbound email (still to be chosen for FR-003/FR-010) are three separate panels for a solo, first-time developer to manage.
5. `wrangler rollback` reverts Worker code only — never Supabase schema migrations — so a code rollback after a combined migration+code deploy can leave the app reading a database it no longer matches.

### Pre-Mortem — How This Could Fail

The team deployed the parish site on Cloudflare Workers. Six months later the decision looked like a mistake. Learning Astro, Supabase, and Cloudflare simultaneously, the developer assumed production would behave exactly like `astro dev`. The profanity-filter logic, grown slightly heavier than in testing, began intermittently exceeding the free tier's 10ms CPU budget under unexpected load (a shared-link spike, still under the 3-submissions/day cap per source but from many sources at once); the Worker silently cut the request short with no readable error, and parishioners reported the form "sometimes does nothing." The developer spent a week suspecting an Astro bug before realizing it was the CPU limit, because free-tier `wrangler tail` output didn't surface the limit breach clearly. Meanwhile, a database migration (a new column) and a code deploy landed in the same session; after finally spotting the real bug, the developer ran `wrangler rollback` out of habit — which reverted the code but not the migration, so the rolled-back code tried to read a column that no longer existed in the expected shape, compounding the outage instead of fixing it.

### Unknown Unknowns

- `wrangler rollback` never touches Supabase migrations or secrets set after that deploy — sequencing (compatible migration first, code second) has to be a manual discipline, not something the tooling enforces.
- The free tier's CPU limit is per-request, not per-session — a query like "check this source's recent urgent-flag history" (from the PRD's business logic) can silently approach the limit as the submissions table grows, especially without a good index.
- Cloudflare Workers read env vars via `Astro.locals.runtime.env`, not the `import.meta.env` pattern used by other Astro adapters — copying examples from Vercel/Netlify tutorials is a common, silent source of bugs.
- `astro:env/server` (already used in `src/lib/supabase.ts`) and the Wrangler bindings in `wrangler.jsonc` must be kept in sync by hand; adding one new secret means editing both places.
- The free tier's 100,000-request ceiling is **per day**, not per month — generous at this project's scale, but worth reading literally rather than as "unlimited."

## Operational Story

- **Preview deploys**: Cloudflare Pages' automatic PR-preview URLs don't apply here — the scaffold uses the Workers path (`wrangler deploy`), which has no built-in per-PR preview yet. A preview flow would need either Wrangler Versions (`wrangler versions upload` + `wrangler versions deploy`) or a separate named environment in `wrangler.jsonc`; neither is currently configured. Treat this as a follow-up, not something this research sets up (CI/CD wiring is out of scope for this skill).
- **Secrets**: `SUPABASE_URL` / `SUPABASE_KEY` live as Wrangler secrets in the Cloudflare dashboard (or via `wrangler secret put`), separate from the `.env`/`.dev.vars` files used for local dev. Only whoever has Cloudflare account access can read or rotate them; rotate by re-running `wrangler secret put <NAME>` and redeploying.
- **Rollback**: `wrangler rollback [deployment-id]` reverts the Worker's code instantly. It does **not** revert Supabase schema migrations or secrets — see the risk register below for the mitigation.
- **Approval**: A human must run the first `wrangler login` (interactive OAuth) and any Cloudflare billing-tier change. Routine `wrangler deploy` / `wrangler rollback` / `wrangler tail` can be run unattended by an agent once secrets are set, since none of them touch billing or destroy data.
- **Logs**: `npx wrangler tail` streams live production logs read-only from the CLI; historical request analytics are available in the Cloudflare dashboard (no CLI history command as of this research).

## Risk Register

| Risk | Source | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| Supabase free tier has no automated backups or PITR (true PITR is $100/mo per 7-day window, out of budget) | Research finding | M | H | Weekly `supabase db dump` via a free scheduled GitHub Action, stored as a private artifact; document a manual restore procedure. This is identical under any deploy platform — Supabase is external to all of them. |
| `wrangler rollback` reverts Worker code but never Supabase migrations, risking a code/schema mismatch after a combined deploy | Pre-mortem | M | H | Always ship backward-compatible migrations before the code that depends on them; never roll back Worker code past a migration boundary without also restoring the DB from the Step-1 backup. |
| Free-tier 10ms CPU/request limit could silently throttle the profanity/urgency-frequency business logic | Devil's advocate | L–M | M | Keep the filter lightweight (small dictionary/regex, no heavy NLP dependency); verify with `npm run smoke` against a real deploy before launch; watch `wrangler tail` for CPU-limit errors. |
| Community-reported (unverified) `nodejs_compat`/`process`-polyfill interaction with Astro's Node detection | Research finding (unverified) | Unknown | M | Run `npm run smoke` against a live deployed preview before trusting env/session behavior in production; if issues appear, add `disable_nodejs_process_v2` to `compatibility_flags` in `wrangler.jsonc`. |
| Env var access pattern (`Astro.locals.runtime.env`) differs from other Astro adapters and must stay manually synced with `astro:env/server` | Unknown unknowns | M | L–M | Keep `src/lib/supabase.ts` as the single place that reads Supabase env vars; add any new var to both `wrangler.jsonc`/`.dev.vars` and `astro.config.mjs`'s `env.schema` together. |
| CI (`@.github/workflows/ci.yml`) currently only lints/type-checks/builds/smoke-tests — it does not run `wrangler deploy`, despite `tech-stack.md`'s `ci_default_flow: auto-deploy-on-merge` hint. It also only triggers on branch `master`, while the repo's actual branch is `main`. | Research finding (own inspection) | Certain | L | Out of scope for this research (CI/CD setup) — flag for whoever wires up deploy automation: fix the branch name and add an explicit `wrangler deploy` step, gated on a human-reviewed merge to avoid an agent silently deploying broken code. |
| `wrangler.jsonc`'s `name` field still reads the starter's default (`10x-astro-starter`), not the project name | Research finding (own inspection) | Certain | L | Rename to `parish-website` (or similar) before the first real production deploy — this becomes the Worker's subdomain. |

## Getting Started

1. Authenticate once (human step): `npx wrangler login`.
2. Rename the Worker in `wrangler.jsonc` (`name` field) from `10x-astro-starter` to `parish-website` before the first real deploy.
3. Set production secrets from the Supabase project's Settings → API: `npx wrangler secret put SUPABASE_URL` and `npx wrangler secret put SUPABASE_KEY`.
4. Build and deploy: `npm run build && npx wrangler deploy`.
5. Verify against the live URL with the project's existing smoke test: `BASE_URL=https://<worker-name>.<subdomain>.workers.dev npm run smoke`.
6. Set up the free database-backup mitigation from the risk register: a scheduled GitHub Action running `supabase db dump` against the production project, stored as a private artifact.

## Out of Scope

The following were not evaluated in this research:
- Docker image configuration
- CI/CD pipeline setup (including wiring an actual `wrangler deploy` step into `.github/workflows/ci.yml`, and fixing its `master`/`main` branch mismatch)
- Production-scale architecture (multi-region, HA, DR)
- A full re-evaluation of the data-layer choice (Supabase vs. Cloudflare D1) — the developer chose to keep the current stack unchanged; revisit via `/10x-tech-stack-selector` if this becomes a blocker later.
