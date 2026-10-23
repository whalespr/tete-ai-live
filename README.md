# TETE AI — Live Draw Predictions Website

**What this is:** A live website that shows high-probability full-time draw predictions every day. No coding needed to run it — just deploy and it updates itself.

**How it works:** A backend (`/api/predictions`) pulls today's fixtures + odds from The Odds API, enriches with history, runs a 6-signal draw ensemble, and returns JSON. The frontend (`index.html`) fetches from that API and renders draw-first cards, tables and odds. The API is warmed daily at 07:00 UTC by Vercel Cron and cached (1h). If live fetch fails, it falls back to demo fixtures automatically.

---

## Deploy to a live URL in 3 steps (no coding)

You need a free Vercel account (vercel.com — sign in with Google/GitHub).

### 1. Push to GitHub → Import to Vercel

- On GitHub, create a new empty repository (e.g. `tete-ai-live`).
- Upload the entire `tete-ai-live` folder to it (drag & drop on github.com works).
- Go to vercel.com → **Add New → Project** → **Import** your `tete-ai-live` repo → **Deploy**.

You get a live URL instantly, e.g. `https://tete-ai-live.vercel.app` — open it, you’ll see demo fixtures right away.

### 2. Add your free API keys (to go live)

Get two free keys (5 minutes total):

- **The Odds API** — https://the-odds-api.com — 500 requests/month free. Sign up, copy the key.
- **API-Football** — https://www.api-football.com (api-sports.io) — 100/day free. Sign up, copy the key.

Then in Vercel: **Project → Settings → Environment Variables** → add:

- `ODDS_API_KEY` = your The Odds API key
- `API_FOOTBALL_KEY` = your API-Football key
- `API_FOOTBALL_HOST` = `v3.football.api-sports.io`

Hit **Save**, then **Deployments → Redeploy** (or push any tiny commit). The site is now live with real bookmaker odds. Without keys it stays in demo — so you can deploy first and add keys later.

### 3. Auto-updates — already wired

`vercel.json` has a Cron: `0 7 * * *` → `GET /api/predictions?warm=1` — warms the cache every morning. The frontend always fetches from `/api/predictions` (same origin, no CORS issues). Cache is `s-maxage=3600, stale-while-revalidate=7200`, so it’s fast and never blocks a visitor.

No manual refresh needed. Visitors can still hit **Refresh** to bust the cache.

---

## Custom domain (one click in Vercel)

**Project → Settings → Domains** → type your domain (e.g. `tete-ai.com`) → follow the DNS instructions Vercel shows (add a `CNAME` or `A` record at your registrar). Vercel auto-provisions HTTPS. No code changes needed.

If you want `tete-ai.com`, buy the domain at any registrar (Namecheap, Google Domains, Cloudflare) and point it to Vercel there.

---

## Local preview (optional)

Just double-click `index.html` — it works as a standalone file with demo fixtures, no server needed. For the live API flow, run a local dev server that serves the Vercel function:

```bash
# with Vercel CLI
npm i -g vercel
vercel dev
# → http://localhost:3000  (API + frontend together)
```

Or any static server for the demo-only preview: `npx serve .` or `python -m http.server 8000`.

---

## Structure

- `index.html` — the site (fetches from `/api/predictions`, falls back to in-page demo engine)
- `api/predictions.js` — serverless function: live Odds API → 6-signal draw ensemble → JSON (keys never hit the browser)
- `vercel.json` — Cron + cache headers + function config
- `.env.example` — which env vars to set in Vercel
- `package.json` — minimal (Node ≥18, no extra deps for the API)

---

## If something goes wrong

- **Site shows demo after you added keys:** Check Vercel → Settings → Environment Variables → that `ODDS_API_KEY` is set for **Production**, then **Redeploy**.
- **Odds API quota hit:** Free tier is 500/mo. The site fetches 8 leagues per refresh (~8 credits). Use **Refresh** sparingly and rely on the daily Cron.
- **Custom domain not working:** DNS can take minutes to hours to propagate. Vercel’s domain page shows status.

---

Built for TETE AI — Draw Edition v2.0
