# Changelog

All notable changes to the Total Return Calculator are documented in this file.

## [Unreleased]

### Changed
- **Colour picker palette overhauled for better distinctness.** The previous 36-colour palette was built from groups of 3 near-identical shades per hue (e.g. three very similar reds in a row), making adjacent swatches hard to tell apart. Replaced with 40 colours built from 20 hues spaced evenly around the colour wheel (18° apart), each offered in a vivid tone and a lighter/pastel tone. Verified numerically: the worst-case distance between adjacent swatches in the old palette was as low as ~45 (RGB Euclidean distance); every adjacent pair in the new palette is 80+ apart, a near-doubling of perceptual separation. The picker popup was widened and the swatch grid changed from 8 to 10 columns to comfortably fit the larger set.

### Changed
- Chart fonts (axis ticks and tooltip title/body text) increased by 50% to match the rest of the interface's font scaling.
- Ticker summary cards: the dividing line beneath the ticker symbol was moved to sit after the full company name instead of directly under the ticker symbol, so it properly separates the card's header (ticker + name) from the data rows below rather than visually cutting between the symbol and its name. Cards with no resolved company name still show the divider directly under the ticker symbol.

### Fixed
- **Critical: entire page failed to initialize (ticker fields missing, colour swatches blank).** The `activeIndices` state variable was declared with `let` far later in the script than where `loadTheme()` first referenced it during page load. Because `let`/`const` bindings are not accessible before their declaration line runs (the "temporal dead zone"), this threw an uncaught `ReferenceError` immediately on page load, which silently halted all subsequent script execution — including the call to `buildTickers()`. This is why the ticker input fields disappeared entirely and colour picker swatches rendered with no fill. Fixed by moving the `activeIndices` declaration to the top of the script alongside the other state variables, before any initialization code can reference it. Verified by executing the full page in a real DOM environment (jsdom) and confirming ticker fields, colour dots, and swatches all populate correctly.
- Y-axis moved to the right-hand side of the chart per request, using Chart.js's `position:'right'` option. Crosshair and dividend/split vertical-line plugins required no changes since they read the axis's computed boundaries dynamically.

### Added
- **Dark/light mode toggle.** A button in the header (🌙 Dark / ☀️ Light) switches the entire UI between a dark theme (default) and a newly designed light theme. All colours — backgrounds, borders, text, tooltips, the CORS notice/error boxes, and the chart itself (axes, gridlines, crosshair, dividend/split lines) — respond to the active theme. Preference is saved to `localStorage` independently of other settings and persists across sessions; it is not affected by the Clear button.
- **All font sizes increased by 50%** across the entire interface for improved readability, including every label, input, button, card row, tooltip, and table cell.

### Fixed
- Chart axis gridline/border colours were previously passed to Chart.js as literal `"var(--name)"` strings, which the HTML canvas API cannot interpret (canvas has no concept of CSS custom properties). This silently fell back to a default/incorrect colour. Axis colours are now resolved to actual computed hex values via a `getCssVar()` helper before being handed to Chart.js, and re-resolved on every theme switch.

### Fixed
- **Split-direction sanity check (reverse split misparse).** Yahoo Finance's split event data can report an inverted ratio for reverse splits — confirmed on real-world tickers like YieldMax ETFs (e.g. AMDY's December 2025 1-for-5 reverse split), where the calculator previously multiplied share count by 5x instead of dividing, producing wildly incorrect total return figures. The calculator now cross-validates every parsed split ratio against the actual day-over-day price movement on the split date. If the parsed ratio and the observed price jump don't move inversely (as a value-neutral split always should), the ratio is automatically corrected by taking its reciprocal. Affected tickers display a visible warning badge on their summary card, and a status bar notice lists every ticker where a correction was applied.

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
