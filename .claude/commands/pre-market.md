# Pre-Market Workflow

**When:** 8:00 AM CT (90 minutes before the 9:30 ET / 8:30 CT regular session open)
**Cron:** `0 8 * * 1-5` (America/Chicago)

---

## Step 1 — Read Strategy

Read `memory/TRADING-STRATEGY.md` in full.

## Step 2 — Check Market Calendar

```
GET https://paper-api.alpaca.markets/v2/clock
Headers: APCA-API-KEY-ID: $ALPACA_API_KEY, APCA-API-SECRET-KEY: $ALPACA_SECRET_KEY
```

If `is_open` is false and `next_open` is not today → market is closed today. Log it and stop.

## Step 3 — Check Account Status

```
GET https://paper-api.alpaca.markets/v2/account
```

Log: portfolio value, buying power, cash. If `trading_blocked` or `account_blocked` is true → log and stop.

## Step 4 — Determine Trend

```
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=1Day&limit=60&feed=iex
```

Analyze the last 60 daily bars. Compare the first close to the last close, and check whether recent highs and lows are trending up, down, or sideways. (Weekly timeframe not available on free IEX feed — use 60 daily bars as the trend filter instead.)

- **UPTREND** → bias long setups today
- **DOWNTREND** → bias short setups today
- **SIDEWAYS** → either direction, minimum share size only

Log the trend conclusion.

## Step 5 — Identify Key Levels

```
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=15Min&limit=50&feed=iex
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=1Day&limit=10&feed=iex
```

From this data identify:
- **Prior day high and low** (yesterday's session)
- **Current pre-market high and low** (from the 15m bars before open)
- **Round number levels** near current price (every $5: $550, $555, etc.)
- **Swing highs and lows** from the 15m chart over the last 5 days
- **Any obvious support/resistance clusters** where multiple touches occurred

## Step 6 — Check Economic Calendar

Search: "economic calendar today {date}" to find any major reports scheduled (FOMC, CPI, NFP, Jobs). If a high-impact report is scheduled between 9:00–10:30 AM ET, note it — the market-open routine will need to wait until after the report.

## Step 7 — Write Research Log

Append to `memory/RESEARCH-LOG.md`:

```
## Pre-Market — {YYYY-MM-DD}
**Trend (weekly):** UPTREND / DOWNTREND / SIDEWAYS
**Bias today:** LONG / SHORT / BOTH

**Account balance:** $XX,XXX.XX
**Buying power:** $XX,XXX.XX

**Key levels:**
- Prior day high: $XXX.XX
- Prior day low: $XXX.XX
- Pre-market high: $XXX.XX
- Pre-market low: $XXX.XX
- Key resistance: $XXX.XX, $XXX.XX
- Key support: $XXX.XX, $XXX.XX
- Round numbers nearby: $XXX, $XXX

**Economic reports today:** [None / "CPI at 9:30 ET — wait until 10:00 ET"]

**Trade plan:**
- Primary setup to watch: [e.g., "Bullish reversal at $548 prior day low if SPY dips there"]
- Conditions to sit out: [e.g., "If SPY gaps up >$3 at open, no clean entry — skip"]
```

## Step 8 — Commit Memory

```
git add memory/
git commit -m "bot: pre-market {YYYY-MM-DD}"
git push origin main
```
