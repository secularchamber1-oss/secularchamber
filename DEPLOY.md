# Put SECULAR CHAMBERS live (free: GitHub + Cloudflare)

About 45 minutes the first time. After that, every change you push to GitHub
goes live by itself in 2–3 minutes.

**What you need:** your GitHub account, a free Cloudflare account, a card for
Cloudflare's free file storage (not charged within the free 10 GB), and access
to the domain registrar for `secularchambers.com`.

---

## Part 1 — Put the code on GitHub (10 min)

Easiest with **GitHub Desktop** (desktop.github.com, free):

1. Open GitHub Desktop → sign in with your GitHub account.
2. **File → Add Local Repository…** → choose `Documents/secular-chambers`.
3. Click **Publish repository** → name `secular-chambers` → keep **"Keep this code private"
   ticked** → Publish.

(Terminal alternative: create an empty *private* repo `secular-chambers` on
github.com, then in this folder run
`git remote add origin https://github.com/YOUR-NAME/secular-chambers.git` and
`git push -u origin main`.)

## Part 2 — Cloudflare database and storage (10 min)

1. Sign up free at **dash.cloudflare.com**.
2. **Storage & Databases → D1 SQL Database → Create** → name it
   `secular-chambers-db` → Create. Copy the **Database ID** (looks like
   `1a2b3c4d-…`). You need it in Part 3.
3. **Storage & Databases → R2 Object Storage** → turn it on (asks for a card;
   free up to 10 GB) → **Create bucket** → name `secular-chambers-files` →
   Create. Leave public access **off**.

## Part 3 — Connect GitHub so it deploys itself (10 min)

1. **Workers & Pages → Create → Import a repository** → connect GitHub → pick
   `secular-chambers`.
2. Fill in:
   - Project name: `secular-chambers`
   - **Root directory (advanced):** `chamber-app`
   - **Build command:** `npm ci`
   - **Deploy command:** `npm run deploy`
   - **Build variables:**
     - `CF_D1_DATABASE_ID` = the Database ID from Part 2
     - `NODE_VERSION` = `22`
3. **Deploy.** The first run creates the database tables, then publishes the
   site at `https://secular-chambers.YOUR-NAME.workers.dev`.
   - If it stops at "migrations" with a permissions error, open the project's
     **Settings → Build → API token** and give it **D1: Edit**, then retry.

## Part 4 — Email sign-in codes (10 min, required)

Everyone, including the owner, signs in with a 6-digit code sent by email.

1. Sign up free at **resend.com** → **Domains → Add** `secularchambers.com` →
   add the DNS records it shows (if the domain is on Cloudflare, Resend can add
   them for you) → wait for **Verified**.
2. **API Keys → Create** → copy the key.
3. In Cloudflare: your Worker → **Settings → Variables and Secrets → Add**, type
   **Secret** for each:
   - `RESEND_API_KEY` = the key
   - `OTP_FROM_EMAIL` = `Secular Chambers <no-reply@secularchambers.com>`
4. Open `…/login`, enter **kanoonwise@gmail.com**, and sign in with the code.
   You're the admin. Add your team in **Cases & team → Team**.

## Part 5 — Instagram feed (optional, 20 min)

developers.facebook.com → **Create app** (Business) → add **Instagram** →
"API setup with Instagram login" → add @secularchamber → **Generate token**.
Add it as a **Secret** named `INSTAGRAM_ACCESS_TOKEN`. The site renews it weekly
by itself. Until then the section shows "Follow us" tiles.

## Part 6 — Your domain secularchambers.com (15 min + waiting)

1. Cloudflare → **Add a domain** → `secularchambers.com` → **Free** plan.
2. At your registrar, change the **nameservers** to the two Cloudflare shows.
   This can take a few hours.
3. Worker → **Settings → Domains & Routes → Add → Custom domain** →
   `secularchambers.com` and again `www.secularchambers.com`.

---

## Never set these on the live site

They exist only for local testing and would weaken security:
`TRUST_SITES_HEADERS`, `OTP_INSECURE_ECHO_FOR_TESTS`, `INSTAGRAM_API_BASE`,
or any `STAFF_EMAILS` value.

To add another owner/admin, either add them in **Team** with role **Admin**
(recommended), or add their email to `STAFF_EMAILS` in
`chamber-app/app/chatgpt-auth.ts` and push.

## Making changes later

Edit, commit, push (GitHub Desktop: **Commit to main → Push origin**).
Cloudflare rebuilds and deploys automatically; database changes in
`chamber-app/drizzle/` are applied on the way.

## Free plan limits (plenty for normal use)

Workers: 100,000 requests/day and 10 ms of processing per request. D1: 5 GB.
R2: 10 GB. If you ever see "exceeded CPU" errors, Workers Paid is $5/month.

## Not moved automatically

Records in the old ChatGPT Sites pilot stay there. Export anything you need
from it before switching your announcements to the new site.

## Run it on your Mac (optional)

```bash
cd chamber-app && npm ci && npm run build && npm run db:migrate:local
node --import ./scripts/sites-env.mjs ./node_modules/wrangler/bin/wrangler.js dev --config dist/server/wrangler.json --local --persist-to .wrangler/state --ip 127.0.0.1 --var OTP_INSECURE_ECHO_FOR_TESTS:1
```
Open http://127.0.0.1:8787/login. Locally the sign-in code is filled in for
you, so no email is needed.
