# Project Context

## What This Is

An autonomous SPY day-trading bot powered by Claude Code scheduled routines. It trades a **paper account** on Alpaca Markets. No real money is at risk.

## Why SPY on Alpaca

SPY tracks the S&P 500 and has clean price action that works perfectly with reversal candle strategies on 5m/15m charts. Alpaca has a free paper account with a clean REST API — no funding required to get started.

## Architecture

1. **Five scheduled routines** fire at market-relevant times (see CLAUDE.md)
2. Each routine reads `memory/TRADING-STRATEGY.md` for the rules
3. Market data comes from Alpaca's data API (IEX feed, real-time)
4. Orders execute through Alpaca's paper trading API
5. Memory files updated and committed to git after every run

## Environment Variables Required

Set these on each Claude Code routine (NOT in a .env file):

| Variable | Description |
|----------|-------------|
| `ALPACA_API_KEY` | Alpaca paper account API key ID |
| `ALPACA_SECRET_KEY` | Alpaca paper account secret key |

## Memory-in-Git

Every routine commits updated memory files to git after running. This gives the agent persistent state across sessions. The `memory/` folder is the bot's brain between runs.
