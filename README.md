# DealPoint website — automatic morning deploy

One-time setup (about 15 minutes). After this, every morning's M&A deals brief
publishes itself to the Recent Deals section of dealpointbusinessbrokers.com —
no manual uploads.

## What you do (3 steps)

1. **GitHub account + repo.** Sign up at github.com if needed, then create a new
   repository named `dealpoint-website` (public or private, either works).
   Leave it empty.

2. **FTP secrets.** In the repo: Settings → Secrets and variables → Actions →
   New repository secret. Add three:
   - `FTP_HOST` — your hosting server IP (currently `132.148.178.194`; confirm
     in GoDaddy → Web Hosting → your plan)
   - `FTP_USERNAME` — your GoDaddy FTP username
   - `FTP_PASSWORD` — your GoDaddy FTP password

   Tip: in GoDaddy cPanel → FTP Accounts, create a dedicated FTP account for
   this instead of using your main login. Leave the account's Directory at
   the default home directory — do NOT lock it to `public_html`. The deploy
   workflow already uploads into `public_html/`, so an account locked there
   would land the homepage at `public_html/public_html/` by mistake.

3. **Token for the morning updater.** GitHub → Settings (your account) →
   Developer settings → Personal access tokens → Fine-grained tokens →
   Generate new token:
   - Repository access → Only select repositories → `dealpoint-website`
   - Permissions → Contents → Read and write

   Then tell Rana — you'll get a secure form to paste the token into. It goes
   to secure storage; it never appears in chat.

## What happens after

- Rana pushes the site files and the deploy workflow to the repo.
- Every morning after the deals brief generates (~7:10 AM ET), the Recent Deals
  section is rewritten with the day's deals, pushed to the repo, and GitHub
  Actions uploads the new homepage to your hosting automatically.
- The workflow only ever uploads `public/index.html` → `public_html/index.html`.
  It never deletes anything and touches no other settings.

## Files in this folder

- `public/index.html` — the full site (self-contained; images embedded),
  Recent Deals section relabeled as a market deals feed and seeded with the
  latest brief. The morning updater rewrites everything between the
  `DEALS-FEED-START` / `DEALS-FEED-END` markers.
- `.github/workflows/deploy.yml` — the deploy workflow.

## Recent Deals publishing rule (Chuck, 2026-10-20)

Every deal published to the Recent Deals feed must include, for each deal:
the **price**, the **multiples** (EV/EBITDA and/or EV/Revenue), the underlying
revenue/EBITDA when disclosed, and deal **terms** (cash/stock, earn-out,
rollover) when disclosed. If a figure was not disclosed, print "Not disclosed"
for that field — never estimate or invent one. The same rule is written into
the `DEALS-FEED-START` comment in `public/index.html` and into the daily
M&A brief and LinkedIn post job instructions.
