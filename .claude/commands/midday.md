# Midday Workflow

**When:** 11:30 AM CT (12:30 PM ET)
**Cron:** `30 11 * * 1-5` (America/Chicago)

---

## Step 1 — Read Context

Read:
- `memory/TRADING-STRATEGY.md` — trailing stop rules and exit rules
- `memory/TRADE-LOG.md` — find any trade marked OPEN from today

## Step 2 — Confirm Market Is Open

```
GET https://paper-api.alpaca.markets/v2/clock
Headers: APCA-API-KEY-ID: $ALPACA_API_KEY, APCA-API-SECRET-KEY: $ALPACA_SECRET_KEY
```

If not open → log and stop.

## Step 3 — Check Current SPY Position

```
GET https://paper-api.alpaca.markets/v2/positions/SPY
```

If no position exists → skip to Step 6 (look for new setup).

From the response note:
- `avg_entry_price` — your entry
- `qty` — shares held (positive = long, negative = short)
- `unrealized_pl` — current P&L in dollars
- `current_price` — latest price

## Step 4 — Apply Trailing Stop Milestones

Compare `unrealized_pl` against the milestones from TRADING-STRATEGY.md:

| If unrealized_pl ≥ | New stop should lock in |
|--------------------|------------------------|
| $30 | $0 (breakeven = entry price) |
| $50 | $15 |
| $80 | $45 |
| $110 | $75 |

Calculate the new stop price:
```
For long:  new_stop = entry + (locked_profit / shares)
For short: new_stop = entry - (locked_profit / shares)
```

If the current stop (from TRADE-LOG) is already at or better than the new level → no action needed.

If the stop needs to move:

### 4a. Cancel Existing Stop Order

```
GET https://paper-api.alpaca.markets/v2/orders?status=open&symbols=SPY
```

Find the stop order. Cancel it:
```
DELETE https://paper-api.alpaca.markets/v2/orders/{stop_order_id}
```

### 4b. Place New Stop at Updated Level

```json
POST https://paper-api.alpaca.markets/v2/orders
{
  "symbol": "SPY",
  "qty": "{SHARES}",
  "side": "sell",
  "type": "stop",
  "time_in_force": "day",
  "stop_price": "{NEW_STOP_PRICE}"
}
```

Update `memory/TRADE-LOG.md` with new stop price, milestone reached, and new stop order ID.

## Step 5 — Check for Thesis Break

```
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=5Min&limit=10&feed=iex
```

Look at the last 3–4 five-minute candles:
- **Long position:** Did a 5m candle close BELOW the support level that triggered the entry? If yes → thesis broken → exit now.
- **Short position:** Did a 5m candle close ABOVE the resistance level? If yes → exit now.

To exit early (thesis break):
1. Cancel the stop order: `DELETE /v2/orders/{stop_order_id}`
2. Close the position: `DELETE https://paper-api.alpaca.markets/v2/positions/SPY`
3. Log exit in TRADE-LOG with reason "Thesis invalidated — closed through key level"

## Step 6 — Look for a New Setup (if no position)

If no SPY position is open and time is before 2:30 PM CT:

Run the same scan as market-open (Steps 4–10 of the market-open routine). Only enter if all gate conditions pass and a clean setup is present. Do not force a trade just because it's midday.

## Step 7 — Commit Memory

```
git add memory/
git commit -m "bot: midday {YYYY-MM-DD}"
git push origin main
```

Log: "Midday complete. Position: {open/none}. Unrealized P&L: ${amount}. Stop updated: {yes/no}"
