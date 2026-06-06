# Trading Strategy

## Overview

I trade **SPY (S&P 500 ETF)** as a day trader. My edge is identifying reversal candle patterns at key levels on the 5-minute and 15-minute charts, entering on the 1-minute chart for precision, always aligned with the weekly trend direction.

I risk $100 per trade with a 2:1 minimum reward-to-risk ratio. I use a trailing stop to let winners run while locking in profit at milestones. The goal is high-probability setups with tight, defined risk.

---

## Instrument

- **Symbol:** SPY only
- **No other symbols** — ever

---

## Trend Filter (Non-Negotiable)

Before taking any trade, determine the macro trend using SPY weekly bars:

- **Uptrend:** Recent weekly candles making higher highs and higher lows → take LONG setups only (or both, but strongly favor longs)
- **Downtrend:** Recent weekly candles making lower highs and lower lows → take SHORT setups only (or both, but strongly favor shorts)
- **Sideways/choppy:** Either direction fine, but stay at minimum share size

This check is done every pre-market and carried into the day's bias.

---

## Pre-Market Preparation

Each morning before the open, identify **key levels** on SPY:

- Prior day high and low
- Overnight (pre-market) high and low
- Major round numbers near current price (every $5: $550, $555, $560...)
- VWAP (beginning of day anchor)
- Visible support/resistance clusters from the 15-min chart (swing highs/lows from last 5 days)

All levels logged in `memory/RESEARCH-LOG.md` before the open.

---

## Timing Rules

- **NEVER trade the first 30 minutes** — wait until at least **9:00 AM CT (10:00 AM ET)**
- **Primary trade window:** 9:00 AM – 11:30 AM CT (10:00 AM – 12:30 PM ET)
- **Second window:** 12:30 PM – 2:00 PM CT (1:30 PM – 3:00 PM ET), only if clean setup
- **NO new entries after 2:30 PM CT (3:30 PM ET)**
- **All positions closed by 2:45 PM CT (3:45 PM ET)** — no exceptions

---

## Entry Rules

### Step 1 — Find the Signal on 5m or 15m Chart

Look for a reversal candle pattern forming **at or near a key level**:

**Bullish setups (at support):**
- Hammer: lower wick ≥ 2× body, closes in upper half
- Bullish Engulfing: green candle body fully covers prior red candle
- Morning Star: red → doji/small body → green (3-candle pattern)
- Bullish Pin Bar: long lower wick rejecting a support level

**Bearish setups (at resistance):**
- Shooting Star: upper wick ≥ 2× body, closes in lower half
- Bearish Engulfing: red candle body fully covers prior green candle
- Evening Star: green → doji → red (3-candle pattern)
- Bearish Pin Bar: long upper wick rejecting resistance

**The signal candle must fully close before entry is considered.** No anticipating.

Patterns that form in the middle of a range with no key level nearby are skipped.

### Step 2 — Confirm on 1-Minute Chart

After the 5m/15m signal candle closes:
- Switch to 1-minute chart
- Wait for 1–2 confirming candles moving in the entry direction
- Long: 1m candles bouncing up, price making higher lows off the support
- Short: 1m candles rejecting down, price making lower highs off resistance

### Step 3 — Enter

- **Order type:** Limit order
- **Long entry:** Limit at or just above the 1m confirming candle's close (within 1–2 cents)
- **Short entry:** Limit at or just below the 1m confirming candle's close

---

## Stop-Loss Placement

- **Long stop:** Below the low of the 5m or 15m signal candle (whichever is lower)
- **Short stop:** Above the high of the 5m or 15m signal candle
- **Maximum stop distance:** $2.00 per share
- If stop would need to be more than $2.00/share away from entry → skip the trade
- Place stop order **immediately after entry fills** — never sit in a naked position

---

## Position Sizing

Risk exactly $100 per trade:

```
shares = $100 / stop_distance_per_share
```

Examples:
- $0.50 stop → 200 shares
- $1.00 stop → 100 shares
- $1.50 stop → 66 shares
- $2.00 stop → 50 shares

Round down to nearest whole share.

---

## Trailing Stop Protocol

Track total position P&L (shares × price change):

| Position P&L | Action |
|-------------|--------|
| +$30 | Move stop to breakeven (entry price) |
| +$50 | Move stop to lock in $15 profit |
| +$80 | Move stop to lock in $45 profit |
| +$110 | Move stop to lock in $75 profit |
| Every additional +$30 | Lock in $15 more |

Stop only moves in the direction of the trade. Never backward.

Cancel the old stop order and replace it each time a milestone is reached.

---

## Exit Rules

- **Primary exit:** Trail the stop — let the trade run until stopped out
- **Minimum target before trailing:** 2:1 reward-to-risk (if stop is $1.00/share → looking for at least $2.00/share gain before the trade earns trailing treatment)
- **Hard time exit:** Close everything by 2:45 PM CT regardless of P&L
- **Thesis break:** If price closes a 5m candle back through the key level that triggered entry → exit immediately, don't wait for stop

---

## When NOT to Trade

- First 30 minutes after open (before 9:00 AM CT)
- After 2:30 PM CT (too close to close)
- If a major economic report is due within the next 30 minutes (FOMC, CPI, NFP, Jobs) — check the calendar in pre-market
- If SPY is moving in a straight line with no clear pullback (no reversal setups will form cleanly)
- If the previous two trades were both losers — stop for the day, review, don't revenge trade

---

## Key Levels to Identify Each Morning

Logged to `memory/RESEARCH-LOG.md` before market open:
- Prior day high/low
- Overnight pre-market high/low
- Round number levels near price ($5 increments)
- Any prior week high/low
- Clear swing highs and lows from the last 5 trading days on the 15m chart

---

## Notes

- Pattern must close on the signal timeframe — no early entries
- If a setup is missed because price moved too far, skip it — another one will come
- Two consecutive losers = done for the day
- Document every trade decision in full — the log is how the strategy improves over time
