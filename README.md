# Signal Desk — Zero-Backend Trading Terminal

A Bloomberg-inspired market research and paper-trading terminal that runs entirely in the browser — no server, no build step, no framework. One HTML file, deployed on GitHub Pages.

**Live:** https://ransomtrader.github.io/Trader/ — click **SETUP → Load demo** to explore instantly with sample data (no API keys required), or add free keys for live streaming markets.

Built iteratively through AI pair-programming (Anthropic's Claude) as an exercise in rapid, research-driven product development: every feature began as a question — *what data exists, is it free, is it reachable from a static page, and does the finance literature say it matters?*

---

## Features

**Market data & charts**
- Real-time price streaming via Finnhub WebSocket (equities + crypto, 24/7 for BTC/ETH/SOL and six more)
- Multi-timeframe candlestick charts (1-minute → weekly) with SMA20/50 overlays, session-anchored VWAP on intraday views, and volume histograms with high-volume highlighting (>1.5× average)
- Marquee ticker strip with in-place live updates and tick-direction flashes
- Sector rotation treemap — tile area proportional to each SPDR sector's move magnitude

**Signals & research**
- Six indicators computed client-side from raw OHLC: RSI-14, SMA 20/50 cross, MACD (12-26-9), Bollinger position, ATR-14, 52-week range — plus SMA200 trend filter and 12-1 momentum
- **Setups scanner** encoding three published, long-evidenced strategies: Connors RSI-2 mean reversion, trend-pullback entries, and Jegadeesh-Titman 12-1 momentum ranking — each hit shows its numeric rationale, suggested entry, ATR-based stop, and earnings-conflict warnings
- Live news tape with keyword sentiment coloring, ticker inference from headline text, per-ticker news, and earnings-calendar badges
- Bloomberg-style command line: `NVDA GP`, `AAPL DES`, `ALRT SPY RSI < 30`, `SCAN`, `HEAT`, `MON`

**Execution & discipline**
- Position sizer built on fixed-fractional risk (1% rule) with ATR-suggested stops and reward-to-risk math
- Simulated execution desk: market and limit paper orders that fill off the live stream, cash/equity accounting, live R-multiples per position
- Trade journal with win rate, average R, and expectancy — closed fills auto-journal; CSV export for external analysis
- Price and RSI alerts, full synthesized audio suite (WebAudio — no assets), PWA installable

## Architecture

Single `index.html` (~2,300 lines). Static hosting on GitHub Pages; all data flows client-side:

| Source | Used for | Cost |
|---|---|---|
| Finnhub REST + WebSocket | Quotes, streaming trades, news, earnings calendar, company profiles | Free tier |
| Twelve Data | OHLC candles, 1-minute through weekly | Free tier |
| SEC EDGAR | Material filings (8-K, 13D, S-1…) surfaced to the tape | Free, official |
| StockTwits | Crowd sentiment gauge | Free |

API keys are user-supplied at runtime and stored only in the browser's localStorage — never committed, never transmitted anywhere except directly to the data providers. The demo mode generates deterministic sample data locally so the full interface is explorable with zero credentials.

Design constraints that shaped the build: free-tier rate limits (request queuing at 8/min for candles, category rotation for news), CORS boundaries (which killed two integrations honestly rather than faking them), and CDN caching (visible build tag in the footer).

## Honest limitations

- Paper fills trigger off last-trade prints — no spread, slippage, or queue modeling, so results skew slightly optimistic versus live execution
- Headline sentiment is keyword-based (~75% directional accuracy), a pre-read hint rather than a signal
- Free news aggregation runs minutes behind professional wires; EDGAR filings partially close that gap by going to the primary source
- Scanner strategies carry modest, documented edges (≈0.1–0.3R expectancy pre-costs) — the tool's real value is enforcing sizing, journaling, and selectivity

## Run locally

```
git clone https://github.com/RansomTrader/Trader.git
```
Open `index.html` in any browser. That's the whole install.

## Disclaimer

Educational tool. Nothing here is investment advice; paper trading only.
