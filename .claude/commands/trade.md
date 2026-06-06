# Trade — Ad-Hoc Command

Manually evaluate or manage a SPY trade. Human provides a direction or action.

Examples: "long SPY", "short SPY", "close SPY", "move stop to $549"

---

## For a New Trade ("long SPY" or "short SPY")

### 1. Read strategy and check account

Read `memory/TRADING-STRATEGY.md`.

```
GET https://paper-api.alpaca.markets/v2/account
GET https://paper-api.alpaca.markets/v2/positions/SPY
GET https://paper-api.alpaca.markets/v2/clock
```

### 2. Fetch charts

```
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=15Min&limit=30&feed=iex
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=5Min&limit=30&feed=iex
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=1Min&limit=15&feed=iex
```

### 3. Apply gate checklist — report each as PASS or FAIL

### 4. Calculate position size

```
stop_distance = abs(entry - stop_price)
shares = floor($100 / stop_distance)
dollar_risk = shares × stop_distance
```

### 5. Present trade plan — wait for human confirmation

```
=== TRADE PLAN ===
Symbol: SPY | Direction: LONG
Current price: $XXX.XX

Pattern: [e.g., Bullish engulfing on 15m at $549 prior day low]
Trend alignment: [UPTREND — matches long]
1m confirmation: [Two green candles bouncing off level]

Entry (limit): $XXX.XX
Stop-loss:     $XXX.XX ($X.XX/share below = $XX total risk)
Target (2:1):  $XXX.XX

Shares: XX | Dollar risk: $XX.XX

Gate: ALL PASS ✓ / [FAILED: reason]

Awaiting your confirmation to place.
```

### 6. Only place after explicit human says to proceed

Entry order:
```json
POST https://paper-api.alpaca.markets/v2/orders
{ "symbol": "SPY", "qty": "{SHARES}", "side": "buy", "type": "limit",
  "time_in_force": "day", "limit_price": "{PRICE}" }
```

Stop order (place immediately after fill):
```json
POST https://paper-api.alpaca.markets/v2/orders
{ "symbol": "SPY", "qty": "{SHARES}", "side": "sell", "type": "stop",
  "time_in_force": "day", "stop_price": "{STOP_PRICE}" }
```

Log to `memory/TRADE-LOG.md`.

---

## For Closing ("close SPY")

```
GET https://paper-api.alpaca.markets/v2/positions/SPY
```

Show current position and P&L. Confirm with human, then:

```
DELETE https://paper-api.alpaca.markets/v2/positions/SPY
DELETE https://paper-api.alpaca.markets/v2/orders  ← cancel any attached stop
```

Log exit to TRADE-LOG.

---

## For Moving a Stop ("move stop to $XXX")

1. Find existing stop: `GET /v2/orders?status=open&symbols=SPY`
2. Verify the new price is still on the correct side of the position
3. Cancel old stop: `DELETE /v2/orders/{order_id}`
4. Place new stop: `POST /v2/orders` with `type: "stop"`, new `stop_price`
5. Log the update to TRADE-LOG
