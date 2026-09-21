---
bootstrapped_at: 2026-09-21T15:09:44Z
starter_id: 10x-astro-starter
starter_name: "10x Astro Starter (Astro + Supabase + Cloudflare)"
project_name: parish-website
language_family: js
package_manager: npm
cwd_strategy: git-clone
bootstrapper_confidence: first-class
phase_3_status: ok
audit_command: "npm audit --json"
---

## Hand-off

```yaml
starter_id: 10x-astro-starter
package_manager: npm
project_name: parish-website
hints:
  language_family: js
  team_size: solo
  deployment_target: cloudflare-pages
  ci_provider: github-actions
  ci_default_flow: auto-deploy-on-merge
  bootstrapper_confidence: first-class
  path_taken: standard
  quality_override: false
  self_check_answers: null
  has_auth: true
  has_payments: false
  has_realtime: false
  has_ai: false
  has_background_jobs: false
```

### Why this stack

A solo builder with zero prior development and AI-agent experience, shipping a parish website over a ~10-week after-hours MVP, needs a battle-tested, agent-friendly starter that ships auth, a database, and edge deployment out of the box rather than assembling them by hand. 10x Astro Starter (Astro + Supabase + Cloudflare) is the recommended default for `(web-app, js)` and clears all four agent-friendly gates, so an AI coding agent can reason about the codebase reliably. The PRD's login-plus-password-recovery requirement (FR-007, FR-013) maps directly onto Supabase auth; payments, realtime, and AI features are out of scope per the PRD. Deployment targets Cloudflare Pages — the starter's own default and the most generous free tier for this project's expected scale (tens to low hundreds of users). CI runs on GitHub Actions with auto-deploy-on-merge, the starter's standard shape.

## Pre-scaffold verification

| Signal             | Value                                       | Severity | Notes                                                              |
| ------------------- | -------------------------------------------- | -------- | ------------------------------------------------------------------ |
| npm package         | not run                                      | n/a      | `cmd_template` starts with `git clone`; no npm CLI package to check |
| GitHub repo         | przeprogramowani/10x-astro-starter last pushed 2026-09-12 | fresh    | from card `docs_url`, checked via GitHub REST API (`gh` CLI unavailable, used `curl` fallback) |

## Scaffold log

**Resolved invocation**: `git clone https://github.com/przeprogramowani/10x-astro-starter .bootstrap-scaffold && cd .bootstrap-scaffold && npm install`
**Strategy**: git-clone
**Exit code**: 0
**Files moved**: 22 top-level entries (21 moved silently, 1 conflict)
**Conflicts (.scaffold siblings)**: CLAUDE.md (existing project CLAUDE.md preserved; starter's version landed at `CLAUDE.md.scaffold`)
**.gitignore handling**: moved silently (no `.gitignore` existed in cwd prior to scaffold)
**.bootstrap-scaffold cleanup**: deleted (including its cloned `.git/`, removed before move-up)

## Post-scaffold audit

**Tool**: npm audit --json
**Summary**: 0 CRITICAL, 0 HIGH, 0 MODERATE, 0 LOW
**Direct vs transitive**: not applicable — 0 findings total (802 dependencies audited: 360 prod, 269 dev, 165 optional, 25 peer)

## Hints recorded but not acted on

| Hint                     | Value          |
| ------------------------ | -------------- |
| bootstrapper_confidence  | first-class    |
| quality_override         | false          |
| path_taken               | standard       |
| self_check_answers       | null           |
| team_size                | solo           |
| deployment_target        | cloudflare-pages |
| ci_provider              | github-actions |
| ci_default_flow          | auto-deploy-on-merge |
| has_auth                 | true           |
| has_payments             | false          |
| has_realtime             | false          |
| has_ai                   | false          |
| has_background_jobs      | false          |

## Next steps

Next: a future skill will set up agent context (CLAUDE.md, AGENTS.md). For now, your project is scaffolded and verified — happy hacking.

Useful manual steps in the meantime:
- `git init` (if you have not already) to start your own repo history.
- Review any `.scaffold` siblings the conflict policy created and decide which version of each file to keep — this run created `CLAUDE.md.scaffold`.
- Address audit findings per your project's risk tolerance — the full breakdown is in this log (currently clean: 0 findings).
