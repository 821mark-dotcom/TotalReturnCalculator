# Changelog

All notable changes to the Total Return Calculator are documented in this file.

## [Unreleased]

## [1.0.0] - Initial Release

### Core functionality
- Compare up to 5 tickers side-by-side (US and TSX, e.g. `AAPL`, `RY.TO`)
- Custom start/end date range picker, with start date defaulting to 1 year ago
- Adjustable initial investment amount, formatted with comma separators
- Four chart views: raw stock price, price return, dividend + split contribution, total return
- Data sourced from Yahoo Finance's unofficial v8 chart API (no backend, no API key)

### DRIP (dividend reinvestment) simulation
- True share-accumulation simulation rather than a simple adjusted-close ratio
- Two parallel share counters: one reinvesting dividends, one price-only
- Dividends reinvested at ex-date closing price
- Stock splits applied to both counters at the correct ratio
- Split ratio validation to guard against malformed Yahoo data (NaN/Infinity protection)
- Cross-check against Yahoo's `adjClose` ratio method, with a divergence percentage shown per ticker and a warning if it exceeds 5%

### After-tax modelling
- Tax rate selector: 0% (TFSA/RRSP), 15%, 25% (default), 40%, or custom
- Dividends are taxed before reinvestment, reflecting real after-tax compounding
- Tax rate displayed per ticker in the summary card

### Chart interactivity
- Crosshair on hover: dotted vertical + horizontal lines following the cursor
- Ctrl+Scroll to zoom (plain scroll preserved for page scrolling), drag to pan, +/- buttons, and a Reset button
- Dividend and split events shown as dotted vertical lines on the chart, colour-coded per ticker, with hover tooltips showing date and amount
- Toggle to show/hide dividend/split lines
- Dollar value ($) vs. percentage return (%) toggle for the chart and cards
- Y-axis automatically rescales to only the currently visible (non-hidden) series

### Customization
- Per-ticker colour picker: 36-colour full-spectrum gradient palette plus a native colour picker for any custom colour
- Clicking the legend for a ticker shows/hides that line on the chart, hides its summary card, and filters it out of the event log
- Chart legend centred, with hover tooltip showing the full company name

### Ticker summary cards
- Clickable ticker symbol linking directly to the ticker's Yahoo Finance page
- Full company name shown beneath the ticker (wraps for long names)
- Currency badge (USD/CAD) — TSX tickers (`.TO` suffix) automatically flagged as CAD
- Separate "Total Return %" and "Total Return $" rows
- Dividend and split event counts with hover tooltips listing every event's date and amount
- CAGR (compound annual growth rate) calculation per ticker

### Event log
- Collapsible table listing every dividend and split event across all tickers, sorted by date
- Shows gross dividend amount, net after-tax amount, shares before/after, and resulting portfolio value
- Respects ticker legend visibility — hidden tickers are excluded from the table
- Updates live when a ticker's colour is changed

### Reference index overlays
- Optional checkboxes to overlay S&P 500, Nasdaq, TSX, VIX, and Bitcoin on the chart
- Rendered as dashed lines, normalized to the same initial investment amount
- Independent of the main ticker DRIP calculations (price-only, no dividend simulation)

### Settings persistence
- All fields (tickers, dates, investment amount, tax rate, colours, chart view, $/% mode, vertical line toggle, active index overlays) automatically saved to `localStorage`
- Settings restored automatically on page reload
- "Clear" button resets all fields to defaults and wipes saved settings

### UI/UX
- Dark, data-terminal aesthetic with a brightened colour palette for readability
- Built-in CORS connectivity test with step-by-step setup instructions and a direct link to a CORS-unblocking browser extension when the test fails
- Responsive layout with explicit grid columns to prevent control wrapping issues
- Inline SVG favicon (candlestick chart icon)

### Known limitations
- No currency conversion — mixing USD and CAD tickers compares native currency values directly in dollar mode (percentage mode is unaffected)
- Relies on Yahoo Finance's unofficial, undocumented API, which has no uptime guarantee and may change without notice
- Requires a CORS-unblocking browser extension when run as a local HTML file, since Yahoo's API does not send CORS headers
- Reference index overlays use price-only return (no dividend reinvestment simulation for indices)
