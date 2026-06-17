# Total Return Calculator

A browser-based, no-backend stock total return comparator. Compare up to 5 tickers
side-by-side — price return, dividend/split contribution, and full DRIP-simulated
total return — with an interactive overlay chart, dollar/% toggle, customizable
colours, and optional reference index overlays (S&P 500, Nasdaq, TSX, VIX, BTC).

## Features

- Compare up to 5 tickers (US and TSX, e.g. `AAPL`, `RY.TO`)
- Custom start/end date range, adjustable initial investment
- True DRIP simulation: dividends reinvested at ex-date close, splits applied correctly
- After-tax dividend reinvestment (0% / 15% / 25% / 40% / custom tax rate)
- Cross-check against Yahoo's adjClose ratio method, with divergence warnings
- Chart views: raw price, price return, dividend+split contribution, total return
- Dollar value or percentage return toggle
- Custom colour picker per ticker (25-colour gradient palette + native picker)
- Clickable ticker symbols linking to Yahoo Finance
- Hover tooltips for dividend/split events, and a full collapsible event log table
- Crosshair + Ctrl+Scroll zoom + drag-to-pan on the chart
- Reference index overlays: S&P 500, Nasdaq, TSX, VIX, BTC (dashed lines)
- Settings persist automatically via `localStorage`
- Clear button to reset all fields

## Data Source

[Yahoo Finance's unofficial v8 chart API](https://query1.finance.yahoo.com/v8/finance/chart/),
fetched directly from the browser. No backend, no API key required.

**Important:** Yahoo's API does not send CORS headers, so a CORS-unblocking browser
extension is required when running this as a local file. Click **⚡ Test CORS** in
the app for setup instructions if you hit a CORS error.

## Usage

1. Download `index.html` (or clone this repo)
2. Open it directly in Chrome (drag the file into a browser window, or `Ctrl+O`)
3. If prompted by the CORS test, install a CORS-unblocking extension (link provided in-app)
4. Enter tickers, a date range, and an investment amount
5. Click **Run Analysis**

No installation, no build step, no server.

## Disclaimer

This tool is for informational and educational purposes only. It is not financial
advice. Calculations are pre-tax-adjustable estimates based on Yahoo Finance data,
which may contain errors or omissions. Always verify figures independently before
making investment decisions.

## License

MIT
