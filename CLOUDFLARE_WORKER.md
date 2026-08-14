# Cloudflare Worker — CORS Proxy for Yahoo Finance

## What is this and why does it exist?

The Total Return Calculator fetches stock data directly from Yahoo Finance's unofficial API.
Yahoo Finance does not send CORS headers in its responses, which means browsers block the
request by default — a security rule called the Same-Origin Policy.

The original workaround was a Chrome extension that strips CORS restrictions.
That approach has two problems:

1. It only works in Chrome on desktop — **iPhones, iPads, Safari, Firefox, and any other
   browser simply cannot use the app.**
2. It requires every user to find, install, and enable a third-party extension.

A **Cloudflare Worker** solves this cleanly. The Worker sits between the browser and Yahoo
Finance, forwards every request, and adds the correct CORS headers on the way back.
The app works on any device and any browser — no extension needed.

Cloudflare Workers are **free** up to 100,000 requests per day (more than enough for
personal use) and require no server management.

---

## How the Worker fits into the app

```
Browser (any device)
    │
    │  fetch(YOUR-WORKER.YOUR-NAME.workers.dev/v8/finance/chart/AAPL?...)
    ▼
Cloudflare Worker   ← adds CORS headers
    │
    │  fetch(query1.finance.yahoo.com/v8/finance/chart/AAPL?...)
    ▼
Yahoo Finance API
```

The Worker simply mirrors the Yahoo Finance URL path and query string, so the app needs
no data-format changes — it still reads the same JSON it always did.

---

## Setting up your own Worker

If you fork or download this app, you need to deploy your own Worker and point the app
at it. Your personal Worker gives you independent request quota and keeps the app
working even if the original Worker is ever taken down.

### Step 1 — Create a free Cloudflare account

Go to **cloudflare.com** and sign up. No credit card is required for Workers.

### Step 2 — Create a new Worker

1. After logging in, click **Workers & Pages** in the left sidebar.
2. Click **Create** → **Create Worker**.
3. Cloudflare gives the Worker a random name (e.g. `late-salad-e369`). You can rename
   it — Worker names must be lowercase letters, numbers, and hyphens only.
4. Click **Deploy** (the default code is fine for now).
5. Then click **Edit code**.

### Step 3 — Paste the Worker script

Delete all the existing code in the editor and paste this entire script:

```javascript
export default {
  async fetch(request) {
    if (request.method === 'OPTIONS') {
      return new Response(null, {
        headers: {
          'Access-Control-Allow-Origin': '*',
          'Access-Control-Allow-Methods': 'GET, OPTIONS',
          'Access-Control-Allow-Headers': '*',
        }
      });
    }

    const url = new URL(request.url);
    const yahooUrl = 'https://query1.finance.yahoo.com' + url.pathname + url.search;

    let resp;
    try {
      resp = await fetch(yahooUrl, {
        headers: {
          'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36',
          'Accept': 'application/json',
        }
      });
    } catch (e) {
      return new Response(JSON.stringify({ error: e.message }), {
        status: 502,
        headers: { 'Content-Type': 'application/json', 'Access-Control-Allow-Origin': '*' }
      });
    }

    const body = await resp.text();
    return new Response(body, {
      status: resp.status,
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'GET, OPTIONS',
      }
    });
  }
};
```

Click **Deploy**.

### Step 4 — Copy your Worker URL

After deploying, Cloudflare shows your Worker's URL at the top of the page.
It looks like:

```
https://YOUR-WORKER.YOUR-NAME.workers.dev
```

Copy this URL — you'll need it in the next step.

### Step 5 — Update index.html

Open `index.html` in a text editor and find these two lines near the top of the
`<script>` block (around line 656):

```javascript
const YAHOO_BASE         = 'https://YOUR-WORKER.YOUR-NAME.workers.dev/v8/finance/chart';
const YAHOO_SUMMARY_BASE = 'https://YOUR-WORKER.YOUR-NAME.workers.dev/v10/finance/quoteSummary';
```

Replace `YOUR-WORKER.YOUR-NAME.workers.dev` with your actual Worker URL.
Keep `/v8/finance/chart` and `/v10/finance/quoteSummary` exactly as-is — only
the domain part changes.

### Step 6 — Test it

Open `index.html` in a browser (or reload if it was already open), enter a ticker,
and click **Run Analysis**. If data loads without an error, the Worker is working.

---

## What the Worker does (in plain English)

- It receives a request from your browser (e.g. `GET /v8/finance/chart/AAPL?...`).
- It rebuilds the same URL but pointed at `query1.finance.yahoo.com`.
- It forwards that request to Yahoo Finance with a desktop browser User-Agent header
  (Yahoo sometimes blocks requests that look like they come from scripts).
- It takes Yahoo's response and sends it back to your browser, adding CORS headers
  so the browser accepts it.

The Worker never stores or logs your data. It is a pure pass-through proxy.

---

## Free tier limits

| Cloudflare Workers Free Plan | Limit |
|---|---|
| Requests per day | 100,000 |
| CPU time per request | 10 ms |
| Monthly cost | $0 |

For personal use, 100,000 requests per day is far more than needed. Each chart load
triggers roughly 1–7 requests depending on how many tickers you have.

---

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| "Network error" on chart load | Wrong Worker URL in index.html | Double-check the URL you pasted |
| Worker URL returns a 404 | Path suffix missing | Make sure `/v8/finance/chart` is still at the end |
| Data loads on desktop but not mobile | Still using old direct Yahoo URL | Confirm both `YAHOO_BASE` lines were updated |
| Worker throws a 502 | Yahoo Finance temporarily blocked the request | Wait a moment and retry |
