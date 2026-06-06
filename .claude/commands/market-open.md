# Market-Open Workflow

**When:** 9:00 AM CT (30 minutes after the 8:30 CT / 9:30 ET regular session open)
**Cron:** `0 9 * * 1-5` (America/Chicago)

---

## Step 1 — Read Context

Read:
- `memory/TRADING-STRATEGY.md` — entry rules and checklist
- `memory/RESEARCH-LOG.md` — today's key levels and bias (pre-market section)

## Step 2 — Confirm Market Is Open

```
GET https://paper-api.alpaca.markets/v2/clock
Headers: APCA-API-KEY-ID: $ALPACA_API_KEY, APCA-API-SECRET-KEY: $ALPACA_SECRET_KEY
```

If `is_open` is false → log "Market not open" and stop.

## Step 3 — Check Account and Positions

```
GET https://paper-api.alpaca.markets/v2/account
GET https://paper-api.alpaca.markets/v2/positions
```

- If a SPY position already exists → skip to Step 7 (manage existing position)
- Note portfolio value and buying power

## Step 4 — Fetch SPY Charts

```
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=15Min&limit=30&feed=iex
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=5Min&limit=40&feed=iex
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=1Min&limit=30&feed=iex
```

## Step 5 — Scan for Reversal Setup

Using the key levels from today's pre-market research log:

**On the 15m chart:**
- Is SPY approaching or sitting at one of the key support/resistance levels?
- Has a bullish or bearish reversal candle pattern fully closed at that level?
- Patterns to look for: Hammer, Bullish Engulfing, Morning Star (long) / Shooting Star, Bearish Engulfing, Evening Star (short)

**On the 5m chart:**
- Does the 5m chart agree? Is there a reversal candle or momentum confirmation in the same direction?

**On the 1m chart:**
- Are the last 2–3 candles moving in the entry direction?
- Long: 1m candles bouncing up from support, green candles, higher lows
- Short: 1m candles rejecting resistance, red candles, lower highs

**If no clean setup is found:** Log "No setup — market-open scan complete" and stop. Another opportunity may appear at midday.

## Step 6 — Apply Gate Checklist

Before documenting or placing anything, confirm every item:

- [ ] Time is after 9:00 AM CT ✓
- [ ] Time is before 2:30 PM CT ✓
- [ ] Market is open ✓
- [ ] Weekly trend matches trade direction (UP for long, DOWN for short, or SIDEWAYS = either)
- [ ] Reversal pattern confirmed on 15m or 5m at a key level
- [ ] 1m chart confirms momentum in entry direction
- [ ] No existing SPY position
- [ ] Stop distance ≤ $2.00/share (identify stop price first, then check)

If any item fails → log which item failed and stop. No trade.

## Step 7 — Calculate Position Size

```
stop_distance = abs(entry_price - stop_price)
shares = floor($100 / stop_distance)
dollar_risk = shares × stop_distance   ← must be ≤ $100
```

If shares < 1 → stop is too wide, skip the trade.

## Step 8 — Document Before Placing

Append to `memory/TRADE-LOG.md` BEFORE placing the order:

```
## {YYYY-MM-DD} {HH:MM CT} — SPY {LONG/SHORT}
**Status:** PENDING
**Pattern:** [e.g., "Hammer on 15m at $548.50 prior day low"]
**Trend alignment:** [UPTREND — long bias matches]
**1m confirmation:** [e.g., "Two green 1m candles bouncing off $548.50"]
**Entry price (limit):** $XXX.XX
**Stop-loss:** $XXX.XX ($X.XX/share below entry)
**Initial target (2:1):** $XXX.XX
**Shares:** XX
**Dollar risk:** $XX.XX
```

## Step 9 — Place Entry Order

```json
POST https://paper-api.alpaca.markets/v2/orders
{
  "symbol": "SPY",
  "qty": "{SHARES}",
  "side": "buy",
  "type": "limit",
  "time_in_force": "day",
  "limit_price": "{ENTRY_PRICE}"
}
```

(Use `"side": "sell"` for a short entry.)

Save the returned `id` (order ID).

## Step 10 — Wait for Fill, Then Place Stop Immediately

Poll for fill:
```
GET https://paper-api.alpaca.markets/v2/orders/{order_id}
```

When `status` is `"filled"`:

```json
POST https://paper-api.alpaca.markets/v2/orders
{
  "symbol": "SPY",
  "qty": "{SHARES}",
  "side": "sell",
  "type": "stop",
  "time_in_force": "day",
  "stop_price": "{STOP_PRICE}"
}
```

(Use `"side": "buy"` stop for a short position.)

Update `memory/TRADE-LOG.md` — change status to OPEN, add fill price and stop order ID.

## Step 11 — Commit Memory

```
git add memory/
git commit -m "bot: market-open {YYYY-MM-DD}"
git push origin main
```
