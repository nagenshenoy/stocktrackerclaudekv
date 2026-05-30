# India Stock Tracker Suite

Unified NSE/BSE dashboard — seven sector/theme trackers in one single-page app with
collapsible sidebar navigation, fixed header, lazy/background loading, dark/light theme,
15-minute auto-refresh, and full Vercel-ready deployment.

## Trackers

| # | Tracker | Accent | Stocks | Exchange |
|---|---------|--------|--------|----------|
| 1 | 🤖 AI & Data Center | Cyan `#00d4ff` | 33 | NSE |
| 2 | ⚡ EV Stocks | Green `#00e87a` | 23 | NSE |
| 3 | 🛢 Energy Stocks | Amber `#ffb347` | 49 | NSE |
| 4 | 🛡 Defence & Aerospace | Violet `#c084fc` | 29 | NSE / BSE |
| 5 | 💊 Pharma & Biotech | Rose `#f472b6` | 39 | NSE |
| 6 | 🏛 Business Houses | Orange `#f59e0b` | 91 | NSE |
| 7 | 🏭 PLI Sectors | Teal `#14b8a6` | 44 | NSE |

## Features

- **Collapsible sidebar** — one-click tracker switching; icon-only mode with tooltips
- **Per-tracker accent theming** — header bar, tabs, badges, charts all update on switch
- **KPI strip** — total stocks, gainers, losers, top mover, avg sector change
- **Sector filter tabs** — filter stock table by sub-sector / segment
- **Sortable stock table** — all columns sortable; 7-day sparkline per row
- **Sector performance cards** — avg change, top / bottom stock per sector
- **Sector heatmap** — 1D / 1W / 1M / 1Y mean returns, sortable columns
- **Multi-period screener** — colour-coded return table, sortable
- **Stock detail modal** — 1D / 5D / 1M / 3M / 1Y price chart, key metrics, NSE link
- **Dark / Light theme** — persisted via localStorage
- **Lazy/background loading** — active tracker loads first; remaining trackers prefetch sequentially
- **Auto refresh** — default On; all trackers refresh every 15 minutes sequentially
- **Responsive dashboard architecture** — fixed top header, scroll-safe cards, table overflow containment
- **IST clock + market status** — NSE open/closed indicator

## Project Structure

```
integrated-tracker/
├── api/
│   └── index.py       # Unified Flask API — all 7 trackers via ?tracker=
├── public/
│   └── index.html     # Complete single-page application
├── requirements.txt
├── vercel.json        # Vercel v2 — @vercel/python + @vercel/static
└── README.md
```

## Local Development

```bash
pip install -r requirements.txt
python api/index.py
# Open http://localhost:5000
```

## Deploy to Vercel

```bash
npm i -g vercel
cd integrated-tracker
vercel --prod
```

## API Endpoints

All endpoints accept `?tracker=ai|ev|en|defence|pharma|bh|pli` (default: `ai`).
The alias `en` maps to `energy` internally.

| Endpoint | Description |
|----------|-------------|
| `GET /api/quotes?tracker=pharma` | Real-time quotes for all stocks |
| `GET /api/sparklines?tracker=ev` | 7-day sparkline close prices |
| `GET /api/sector-heatmap?tracker=defence` | Sector returns 1D/1W/1M/1Y |
| `GET /api/screener?tracker=energy` | Multi-period return screener |
| `GET /api/history?ticker=SUNPHARMA.NS&period=1mo` | OHLCV price history |
| `GET /api/health` | Service health + stock counts per tracker |

> Data via Yahoo Finance (~15 min delayed). Not investment advice.
