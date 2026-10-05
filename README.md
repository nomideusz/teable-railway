# Deploy and Host Teable on Railway

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/new/template/teable-production?utm_medium=integration&utm_source=button&utm_campaign=teable-production)

[Teable](https://teable.ai/) is the open-source Airtable alternative built on real Postgres: spreadsheet-style grids that scale to millions of rows, kanban, gallery, calendar and form views, formulas, links between tables, real-time collaboration, record history, and a REST API with personal access tokens. Your data lives in ordinary Postgres tables you can query with SQL. This template runs the official Teable image with Postgres, Redis and a volume for attachments, with every encryption secret generated for you.

## About Hosting Teable

The stack is three services: Teable, Postgres and Redis.

- **Upstream's own image, pinned.** Teable runs from the official `ghcr.io/teableio/teable` image at a fixed release tag, so a redeploy never surprises you with an untested version.
- **No public default keys.** Teable falls back to encryption keys that are published in its git history when they are not set, including the ones protecting attachment links and API tokens. This template generates every one of them (`SECRET_KEY`, the built-in code sandbox's `SANDBOX_JWT_SECRET` and eight 16-character keys) at deploy time, so Teable boots without its security warning.
- **Attachments on a volume.** Uploaded files are stored on a persistent volume mounted at `/app/.assets`, so they survive redeploys.
- **Redis for realtime and queues.** Teable uses Redis for its cache, background jobs and the real-time sync between browsers. Redis keeps an append-only file on its own volume.
- **Upgrades that migrate themselves.** Every boot runs Teable's database migrations before serving, and Railway's health check on `/health` only switches traffic once they finish.

## Common Use Cases

- Replacing Airtable for a team that wants its data in its own Postgres
- Internal tools: CRMs, content calendars, inventories, applicant trackers and project trackers
- Collecting data with shareable forms and reviewing it in grid or kanban views
- A spreadsheet-like admin UI on top of data that other services read through the API or SQL

## Dependencies for Teable Hosting

- Postgres 17 (included, private network only)
- Redis 8 (included, private network only)

### Deployment Dependencies

- [Teable documentation](https://help.teable.ai/)
- [Teable on GitHub](https://github.com/teableio/teable)
- [Template source on GitHub](https://github.com/nomideusz/teable-railway)

### Implementation Details

**Create your account first.** Open the Teable service's Railway domain and sign up: the first account on a new instance becomes the instance admin. The first boot runs the database migrations, so give it a couple of minutes. If you don't want anyone else to register, turn off sign-ups under Admin settings.

**Back up `SECRET_KEY`.** It signs logins and share links and encrypts stored AI provider keys. Copy it from the Teable service's Variables tab into your password manager. The eight `BACKEND_*_ENCRYPTION_*` values protect attachment links, API tokens, email links and external database URLs; keep them unchanged after the first deploy.

**Custom domain.** After adding one, set `PUBLIC_ORIGIN` to `https://your-domain` so links and attachments use it.

**Email (optional).** Invitations and password resets need SMTP: fill in the `BACKEND_MAIL_*` variables. Railway only allows outbound SMTP on the Pro plan; on other plans use a provider with an HTTPS relay or leave mail off.

**Resources.** Teable runs the app, its plugin server and a built-in code sandbox in one container. In testing it settled at about 1.3 GB of RAM at idle (about 1.6 GB for the whole stack with Postgres and Redis), so use the Hobby plan or above.

## Why Deploy Teable on Railway?

Railway is a singular platform to deploy your infrastructure stack. Railway will host your infrastructure so you don't have to deal with configuration, while allowing you to vertically and horizontally scale it.

By deploying Teable on Railway, you are one step closer to supporting a complete full-stack application with minimal burden. Host your servers, databases, AI agents, and more on Railway.
