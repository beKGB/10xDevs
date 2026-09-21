# TODO — pierwsze wdrożenie na produkcję

Lista rzeczy do zrobienia ręcznie przed i przy pierwszym prawdziwym deployu (Cloudflare Workers). Źródło: `context/foundation/infrastructure.md` i ustalenia z sesji `/10x-infra-research`.

## GitHub — sekrety repo (Settings → Secrets and variables → Actions)

- [ ] Dodaj sekret `CLOUDFLARE_API_TOKEN` (token z uprawnieniem "Edit Cloudflare Workers") — potrzebny nowemu jobowi `deploy` w `.github/workflows/ci.yml`.
- [ ] Dodaj sekret `CLOUDFLARE_ACCOUNT_ID`.
- [ ] Sprawdź, czy `SUPABASE_URL` i `SUPABASE_KEY` są już ustawione jako sekrety repo (używane przez joby `ci` i `deploy`).

## Cloudflare / Wrangler — pierwszy deploy

- [ ] `npx wrangler login` (jednorazowe logowanie przez przeglądarkę).
- [ ] Zmień `name` w `wrangler.jsonc` z `10x-astro-starter` na docelową nazwę (np. `parish-website`) — to będzie nazwa/subdomena Workera w dashboardzie Cloudflare.
- [ ] Ustaw sekrety produkcyjne (dane z Supabase → Settings → API):
  - `npx wrangler secret put SUPABASE_URL`
  - `npx wrangler secret put SUPABASE_KEY`
- [ ] Deploy: `npm run build && npx wrangler deploy`.
- [ ] Zweryfikuj działający deploy smoke testem: `BASE_URL=https://<worker-name>.<subdomena>.workers.dev npm run smoke`.

## Baza danych — mitygacja braku rollbacku na Supabase Free

- [ ] Skonfiguruj cotygodniowy (lub codzienny) darmowy backup: scheduled GitHub Action uruchamiający `supabase db dump` na projekcie produkcyjnym, zapisujący dump jako prywatny artefakt (lub do osobnego storage).
- [ ] Spisz proste instrukcje ręcznego przywracania z takiego dumpa — na wypadek, gdyby trzeba było z niego skorzystać.

## Do rozważenia / opcjonalne porządki

- [ ] Zaktualizuj nieaktualną podpowiedź w `context/foundation/tech-stack.md`: `deployment_target: cloudflare-pages` → `cloudflare-workers` (kosmetyczne, projekt już poprawnie celuje w Workers).
- [ ] Po pierwszym realnym deployu zweryfikuj `npm run smoke`, że flaga `nodejs_compat` w `wrangler.jsonc` nie powoduje cichych błędów (community-reported, niepotwierdzony w oficjalnym changelogu Cloudflare problem z detekcją Node przez Astro) — jeśli coś nie działa, dodaj `disable_nodejs_process_v2` do `compatibility_flags`.
