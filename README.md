# Deploy and Host Twenty on Railway – Open-Source Salesforce Alternative

Twenty is an open-source CRM built as an alternative to Salesforce, with custom objects, pipelines and a modern UI — and no per-seat licensing. This template deploys a self-hosted Twenty instance on Railway so you can manage contacts, companies and deals on infrastructure you control.

[![Deploy on Railway](https://railway.com/button.svg)](https://railway.com/deploy/twenty-salesforce-alternative?referralCode=zxcgoT&utm_medium=integration&utm_source=template&utm_campaign=generic)

## 🚀 Quick Start Deployment Guide

### Step 1: Deploy on Railway
1. Click **Deploy on Railway** above
2. Wait for all four services — server, worker, PostgreSQL and Redis — to finish building

### Step 2: Set your server URL
1. Set `SERVER_URL` to your Railway public domain, with the scheme and no trailing slash
2. The frontend and every invitation and password-reset link are built from it, so a wrong value breaks sign-in in ways that look like unrelated bugs
3. Redeploy after changing it

### Step 3: Check the shared secret
1. `APP_SECRET` is auto-generated
2. Confirm the **same** value is set on both the server and the worker — mismatched secrets leave the app working while background jobs quietly fail

### Step 4: Confirm the worker configuration
1. On the worker service, `DISABLE_DB_MIGRATIONS` and `DISABLE_CRON_JOBS_REGISTRATION` must both be `true`
2. The server already runs migrations and registers cron jobs; duplicating them on the worker causes conflicts

### Step 5: Create your workspace
1. Open your Railway domain
2. Sign up — the first account creates the workspace and becomes its owner
3. Import contacts or create your first records to confirm the database is writable

### Step 6: Close registration and add your team
1. Set `IS_SIGN_UP_DISABLED=true` and redeploy, so strangers cannot register on your CRM
2. Invite teammates from inside the app instead
3. Set `EMAIL_DRIVER=smtp` with your SMTP settings if you want invitation emails delivered

## About Hosting Twenty

This template deploys four services on Railway. The `twentycrm/twenty` image runs twice: once as the **server**, which serves the API and UI and owns database migrations, and once as the **worker**, started with `yarn worker:prod` to run cron jobs, email sync and other background work. PostgreSQL holds every workspace, record and custom object, and Redis backs the job queue shared by the server and worker.

Running it this way gives you a CRM with no per-seat fees — four Railway services at flat compute cost, instead of a bill that grows with headcount. Every record lives in your own Postgres, and attachments live in your own storage (local volume by default, or S3 if you set `STORAGE_TYPE=s3`), so nothing routes through a CRM vendor.

## Common Use Cases

- **Replacing a per-seat CRM** for a growing team, at flat infrastructure cost instead of per-seat pricing
- **Data control and compliance** — keeping customer and pipeline data on infrastructure you control, for privacy or regulatory reasons
- **Custom data modelling** — building custom objects and fields without paying for a higher vendor edition
- **API-first development** — building on the CRM's API without per-call limits imposed by a vendor

## Dependencies for Twenty Hosting

### Deployment Dependencies
- [Twenty (upstream source)](https://github.com/twentyhq/twenty)
- [Twenty developer documentation](https://twenty.com/developers)

## ⚙️ Configuration

| Variable | Required | Description |
|---|---|---|
| `APP_SECRET` | Yes | Signing secret for tokens and sessions. Auto-generated, and it must be identical on the server and the worker or background jobs fail to authenticate |
| `SERVER_URL` | Yes | Your Railway public domain. The frontend and email links are built from it, so a wrong value breaks logins and invitations |
| `PG_DATABASE_URL` | Yes | Postgres connection string, injected from the database service |
| `REDIS_URL` | Yes | Redis connection string, injected from the Redis service |
| `STORAGE_TYPE` | No | `local` by default, writing attachments to the volume. Set to `s3` to use object storage instead |
| `DISABLE_DB_MIGRATIONS` | No | Must be `true` on the worker — the server already runs migrations, and running them twice is asking for trouble |
| `DISABLE_CRON_JOBS_REGISTRATION` | No | Must be `true` on the worker for the same reason |
| `EMAIL_DRIVER` | No | Set to `smtp`, with the matching SMTP settings, to enable email sending and sync |
| `IS_SIGN_UP_DISABLED` | No | Set to `true` after creating your account, or anyone reaching the URL can register |
| `PORT` | No | Set automatically by Railway |

## 🐳 Self-Host with Docker Compose

1. Clone the repository:
   ```bash
   git clone https://github.com/sahilrupani/twenty-crm-railway-template
   cd twenty-crm-railway-template
   ```
2. Copy the environment template:
   ```bash
   cp .env.example .env
   ```
3. Generate a value for `APP_SECRET` and set it in `.env` — use the same value for both the server and worker entries
4. Set `SERVER_URL` in `.env` to the address you'll access the app at (e.g. `http://localhost:3000`)
5. Start the stack:
   ```bash
   docker compose up -d
   ```
6. Open the URL you set as `SERVER_URL` and sign up to create your workspace

## ❓ Frequently Asked Questions (FAQ)

### How much does it cost to run Twenty on Railway?
Four Railway services at flat compute cost. There are no per-seat fees, so adding teammates changes nothing on the bill — which is the main structural difference from Salesforce or HubSpot.

### Is my data private and secure?
Yes. Every record lives in your own Postgres on Railway, and attachments in your own storage. Nothing routes through a CRM vendor.

### Why are there two Twenty services?
The same image runs twice: the server handles the API and UI and owns migrations, while the worker runs background jobs with `yarn worker:prod`. They share `APP_SECRET`, Postgres and Redis.

### Can I stop other people signing up?
Yes — set `IS_SIGN_UP_DISABLED=true` after creating your workspace, then invite teammates from inside the app.

### Can I store attachments in S3?
Yes. Set `STORAGE_TYPE=s3` with the matching credentials instead of using local storage.

### Can I migrate off Railway later?
Yes. It is the standard `twentycrm/twenty` image plus Postgres and Redis — carry a database dump and your storage to any Docker host.

### Why do background jobs never run even though the app works fine?
`APP_SECRET` differs between the server and the worker, or the worker isn't running. Both services need the identical secret.

### Why do login redirects fail, or invitation links point at the wrong host?
`SERVER_URL` is wrong. It must be the full public domain with scheme and no trailing slash, and the app must be redeployed after changing it.

### Why do I see migration errors or duplicated cron jobs on startup?
`DISABLE_DB_MIGRATIONS` and `DISABLE_CRON_JOBS_REGISTRATION` are not set to `true` on the worker. The server owns both responsibilities.

### Why do uploaded attachments disappear?
`STORAGE_TYPE` is `local` with no persistent volume attached. Attach one, or move to `s3`.

### Why do invitation emails never arrive?
`EMAIL_DRIVER` is unset. Set it to `smtp` along with the SMTP credentials.

### Why can anyone create an account on my CRM?
`IS_SIGN_UP_DISABLED` is not set. Enable it once your workspace exists and invite people instead.

## 🛠️ Support & Issues

If you run into problems with this template, open an issue at [https://github.com/sahilrupani/twenty-crm-railway-template/issues](https://github.com/sahilrupani/twenty-crm-railway-template/issues) with a description of the problem, the steps to reproduce it, and any relevant service logs.

---

*This is a community-maintained Railway template for [Twenty](https://github.com/twentyhq/twenty). It is not affiliated with, endorsed by, or supported by the Twenty team or Railway.*