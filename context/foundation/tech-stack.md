---
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
---

## Why this stack

A solo builder with zero prior development and AI-agent experience, shipping a parish website over a ~10-week after-hours MVP, needs a battle-tested, agent-friendly starter that ships auth, a database, and edge deployment out of the box rather than assembling them by hand. 10x Astro Starter (Astro + Supabase + Cloudflare) is the recommended default for `(web-app, js)` and clears all four agent-friendly gates, so an AI coding agent can reason about the codebase reliably. The PRD's login-plus-password-recovery requirement (FR-007, FR-013) maps directly onto Supabase auth; payments, realtime, and AI features are out of scope per the PRD. Deployment targets Cloudflare Pages — the starter's own default and the most generous free tier for this project's expected scale (tens to low hundreds of users). CI runs on GitHub Actions with auto-deploy-on-merge, the starter's standard shape.
