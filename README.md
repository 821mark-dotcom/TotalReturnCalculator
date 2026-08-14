# Total Return Calculator

A browser-based, no-backend stock total return comparator. Two modes:

**Manual Tickers** — compare up to 5 tickers side-by-side with full DRIP simulation: price return, dividend/split contribution, and total return (dividends reinvested after tax).

**ETF Holdings** — load any ETF or fund, pick holdings from its top-10 list, add up to 2 extra comparison tickers, and chart them all together as price return.

Interactive overlay chart, dollar/% toggle, customizable colours, date range presets, crop mode, and optional reference index overlays (S&P 500, Nasdaq, TSX, VIX, BTC).

## Features

### Manual Tickers mode
- Compare up to 5 tickers (US and TSX, e.g. `AAPL`, `RY.TO`)
- True DRIP simulation: dividends reinvested at ex-date close, splits applied correctly
- After-tax dividend reinvestment — defaults to 0% (tax-free); options: 0% / 15% / 25% / 40% / custom
- Cross-check against Yahoo's adjClose ratio method, with divergence warnings
- Split-direction sanity check — auto-corrects inverted Yahoo data for reverse splits
- Full dividend & split event log (collapsible table)

### ETF Holdings mode
- Enter any ETF ticker → loads top holdings from Yahoo Finance
- Select up to 10 individual holdings to chart alongside the fund
- Add up to 2 extra "Compare Against" tickers (e.g. a peer ETF) with colour pickers
- All series use price return (adjClose-based) for apples-to-apples comparison
- Holdings percentage warning when data is incomplete or reflects leveraged exposure
- Link to full Yahoo Finance holdings page per fund

### Chart & UI
- Four chart views: raw price, price return, dividend+split contribution, total return
- Dollar value ($) or percentage return (%) toggle
- Custom colour picker per ticker — 40-colour gradient palette + native colour picker
- Date range presets: 1D / 5D / 1M / 3M / 6M / YTD / 1Y / 3Y / 5Y / 10Y / Max
- ✂ Crop mode: drag to select a time window on the chart, then recalculate from it
- Crosshair, Ctrl+Scroll zoom, drag-to-pan, +/− buttons, and Reset
- Reference index overlays: S&P 500, Nasdaq, TSX, VIX, BTC (dashed lines) — click the legend pill or uncheck the box to remove
- Clickable ticker symbols linking to Yahoo Finance
- Hover tooltips for dividend/split events on the chart
- Dark / light mode toggle, saved independently
- **HowTo** button opens a concise in-app guide (collapses to icon on small screens)
- All settings persist via `localStorage`; Clear button resets everything

## Data Source

Yahoo Finance's unofficial v8 chart API, proxied through a Cloudflare Worker —
no CORS extension required, works on mobile and any browser.

If you fork or self-host this app, you'll need to deploy your own Worker and update
two constants in `index.html`. See [CLOUDFLARE_WORKER.md](CLOUDFLARE_WORKER.md) for
the full explanation, the Worker script, and step-by-step setup instructions.

## Usage

1. Download `index.html` (or clone this repo)
2. Open it in any browser — no server, no install, no build step
3. Choose a mode (Manual Tickers or ETF Holdings), enter tickers and a date range
4. Click **Run Analysis**

## Disclaimer

This tool is for informational and educational purposes only. It is not financial
advice. Calculations are pre-tax-adjustable estimates based on Yahoo Finance data,
which may contain errors or omissions. Always verify figures independently before
making investment decisions.

## License

MIT
