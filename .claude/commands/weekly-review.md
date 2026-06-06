# Weekly Review Workflow

**When:** 3:00 PM CT Friday (after the daily-summary has run and positions are confirmed closed)
**Cron:** `0 15 * * 5` (America/Chicago)

---

## Step 1 — Pull Account Stats

```
GET https://paper-api.alpaca.markets/v2/account
Headers: APCA-API-KEY-ID: $ALPACA_API_KEY, APCA-API-SECRET-KEY: $ALPACA_SECRET_KEY
```

Compare ending balance to last week's ending value (from `memory/WEEKLY-REVIEW.md` most recent entry).

## Step 2 — Gather This Week's Trades

Read `memory/TRADE-LOG.md` — extract all trades from this week (Monday–Friday).
Read `memory/RESEARCH-LOG.md` — extract this week's pre-market plans.

## Step 3 — Calculate Weekly Metrics

- Total trades placed
- Trades filled (some limit orders may not have filled)
- Win / Loss / Breakeven count
- Win rate (%)
- Average winning trade ($)
- Average losing trade ($)
- Largest win ($ and %)
- Largest loss ($ and %)
- Net weekly P&L ($ and %)
- Profit factor = gross wins / gross losses

## Step 4 — Strategy Compliance Check

For each trade this week:
- [ ] Was the entry only after 9:00 AM CT?
- [ ] Was the pattern on a fully closed candle?
- [ ] Was the key level valid (identified in pre-market)?
- [ ] Was the stop placed immediately after fill?
- [ ] Was the position closed before 2:45 PM CT?
- [ ] Was the dollar risk ≤ $100?
- [ ] Did the trade align with the weekly trend?

Flag any violations. Note what happened and why.

## Step 5 — Pattern Analysis

- Which reversal patterns performed best this week?
- Which key levels held / failed?
- Were there clean setups that were missed? Why?
- Any recurring mistake (entering too early, ignoring a bad signal, moving stop too soon)?
- How did the trend filter perform — did trading with the trend produce better results?

## Step 6 — Next Week Outlook

Fetch SPY weekly chart:
```
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=1Week&limit=26&feed=iex
```

- What is the current weekly trend going into next week?
- Are there any major levels nearby on the weekly chart?
- Any known economic events next week (FOMC, CPI, NFP)?

## Step 7 — Write Weekly Review

Append to `memory/WEEKLY-REVIEW.md`:

```
## Week of {DATE} — {Mon DD} to {Fri DD}

**Account value (Monday open):** $XX,XXX.XX
**Account value (Friday close):** $XX,XXX.XX
**Net P&L:** $XX.XX (X.X%)

### Trade Summary
| Date | Side | Entry | Exit | Shares | P&L | Result |
|------|------|-------|------|--------|-----|--------|
| Mon  | Long | $552  | $554 | 50     | +$100 | Win  |

**Win rate:** X of X (X%)
**Avg winner:** $XX.XX | **Avg loser:** $XX.XX
**Profit factor:** X.XX

### Compliance
[Any rule violations this week]

### What Worked
- [Pattern or approach that performed well]

### What Didn't Work
- [Mistake or pattern that underperformed]

### Suggested Strategy Adjustments
- [Pending human approval before editing TRADING-STRATEGY.md]

### Next Week
- **Trend bias:** UPTREND / DOWNTREND / SIDEWAYS
- **Key levels to watch:** $XXX, $XXX
- **Known events:** [FOMC Wed, etc.]
```

## Step 8 — Commit Memory

```
git add memory/
git commit -m "bot: weekly-review {YYYY-MM-DD}"
git push origin main
```
