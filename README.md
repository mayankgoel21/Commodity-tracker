# Commodity Tracker — Netlify Deployment

Fully automated electronics & appliances commodity price tracker.
Prices update automatically on the **1st and 15th of every month** via a Netlify scheduled function.

---

## Deploy in 5 minutes

### Step 1 — Push to GitHub

1. Create a new repo at github.com (name it anything, e.g. `commodity-tracker`)
2. Upload all these files maintaining the folder structure:
   ```
   netlify.toml
   package.json
   public/index.html
   netlify/functions/fetch-prices.mjs
   netlify/functions/prices.mjs
   ```
   Easiest way: drag the whole folder into the GitHub web UI

### Step 2 — Connect to Netlify

1. Go to [netlify.com](https://netlify.com) → **Add new site** → **Import an existing project**
2. Choose **GitHub** → select your repo
3. Build settings are auto-detected from `netlify.toml`:
   - Publish directory: `public`
   - Functions directory: `netlify/functions`
4. Click **Deploy site**

### Step 3 — Enable Netlify Blobs

Netlify Blobs (used to store fetched prices) is enabled automatically on deploy.
No extra configuration needed.

### Step 4 — Trigger the first price fetch

The scheduled function runs on the 1st and 15th automatically, but you need to
trigger it once manually to populate the initial data:

1. In your Netlify dashboard → **Functions** tab
2. Find `fetch-prices` → click **Test function**
3. You'll see a log confirming prices were fetched and stored

Your site is now live at `https://your-site-name.netlify.app`

---

## How it works

```
Every 1st & 15th at 06:00 UTC
        │
        ▼
fetch-prices.mjs (Netlify scheduled function)
        │
        ├── Fetches copper, aluminium, nickel, tin, zinc from IMF PCPS API
        │   (free public API, no key needed)
        │
        ├── Derives: copper_wire = copper × 1.20
        │            stainless = nickel × 0.075 + 1250
        │
        └── Stores result in Netlify Blobs ("commodity-prices" store, key "latest")

Any browser opens the site
        │
        ▼
public/index.html
        │
        └── GET /api/prices → prices.mjs → reads Netlify Blobs → returns JSON
                │
                └── Renders dashboard with live prices
```

## Sharing with your team

Just share the Netlify URL — everyone sees the same live data automatically.
No API key, no setup, no file to download.

## Updating seed data

For commodities without an IMF API (steel, plastics, specialty minerals),
edit the `SEED` object in `netlify/functions/fetch-prices.mjs` and redeploy.
Netlify auto-deploys on every GitHub push.

## Free tier limits

- Netlify free tier: 125k function invocations/month (you'll use ~2/month)
- Netlify Blobs: 1GB storage (you'll use ~10KB)
- IMF PCPS API: unlimited, no auth

Total cost: **$0/month**
