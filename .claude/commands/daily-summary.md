# Daily Summary Workflow

**When:** 2:45 PM CT (3:45 PM ET — 15 minutes before market close at 3:00 PM CT / 4:00 PM ET)
**Cron:** `45 14 * * 1-5` (America/Chicago)

PRIMARY OBJECTIVE: close all SPY positions before the 3:00 PM CT close.

---

## Step 1 — CRITICAL: Close Any Open Position

Run this first, before any analysis.

### 1a. Check for Open Positions

```
GET https://paper-api.alpaca.markets/v2/positions/SPY
Headers: APCA-API-KEY-ID: $ALPACA_API_KEY, APCA-API-SECRET-KEY: $ALPACA_SECRET_KEY
```

If a position exists (any qty ≠ 0):

### 1b. Cancel All Open SPY Orders First

```
GET https://paper-api.alpaca.markets/v2/orders?status=open&symbols=SPY
```

Cancel each open order:
```
DELETE https://paper-api.alpaca.markets/v2/orders/{order_id}
```

### 1c. Close the Position

```
DELETE https://paper-api.alpaca.markets/v2/positions/SPY
```

This closes at market price. Log: "EOD forced close — SPY position closed at ~$XXX.XX"

### 1d. Verify Closed

```
GET https://paper-api.alpaca.markets/v2/positions/SPY
```

Should return a 404 (no position) or empty. If still open, retry the delete.

---

## Step 2 — Pull Final Account State

```
GET https://paper-api.alpaca.markets/v2/account
```

Record:
- End-of-day portfolio value
- Cash
- Buying power

---

## Step 3 — Reconcile Trade Log

Read `memory/TRADE-LOG.md`. Find any trade marked OPEN or PENDING from today.

For each, update to CLOSED:
- Exit price (from the fill or position close)
- P&L = (exit - entry) × shares for long, (entry - exit) × shares for short
- Exit reason: "EOD forced close" / "Stop hit" / "Target hit" / "Thesis invalidated"

---

## Step 4 — Write Daily Summary

Append to `memory/TRADE-LOG.md`:

```
## Daily Summary — {YYYY-MM-DD}
**Starting portfolio value:** $XX,XXX.XX
**Ending portfolio value:** $XX,XXX.XX
**Day P&L:** $XX.XX (X.X%)
**Trades today:** X (X wins / X losses / X breakeven)
**All positions closed:** YES
**Notes:** [e.g., missed a clean morning setup, stop was hit before trailing kicked in, etc.]
```

---

## Step 5 — Commit Memory

```
git add memory/
git commit -m "bot: daily-summary {YYYY-MM-DD}"
git push origin main
```

Log: "Daily summary complete. P&L: ${amount}. All positions closed."
