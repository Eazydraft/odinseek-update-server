# odinseek update server

Cloudflare Worker (fork of Hazel, `cf-workers` variant) that serves the
`electron-updater` generic feed for the odinseek desktop app. It reads GitHub
Releases from `Eazydraft/odinseek` server-side and serves `/files/<channel>.yml`
+ installer redirects at `https://updates.odinseek.com`.

Config lives in `wrangler.toml`:
- `ACCOUNT` / `REPOSITORY` — the GitHub repo whose Releases are served
- `URL` — the public base URL (must match the app's `DESKTOP_UPDATE_SERVER_URL`)
- `TOKEN` — a **secret** (not a var): GitHub PAT with `Contents: Read` on the repo

## Deploy

```bash
npx wrangler@4 login
npx wrangler@4 deploy
npx wrangler@4 secret put TOKEN     # paste a GitHub PAT with Contents:Read on Eazydraft/odinseek
```

## Verify (after CI has cut at least one release)

```bash
curl -I https://updates.odinseek.com/files/stable.yml   # 200 = live; 204 = no release yet
curl https://updates.odinseek.com/                      # overview page
```

Channels are driven by the release version suffix: `1.2.0` → `stable.yml`,
`1.2.0-beta.1` → `beta.yml`, `1.2.0-dev.1` → `dev.yml`.
