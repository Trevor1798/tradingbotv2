# Setup Guide — SPY Day-Trading Bot on Alpaca

## 1. Get Alpaca Paper Trading API Keys

1. Create a free account at [alpaca.markets](https://alpaca.markets)
2. In the dashboard, switch to **Paper Trading** (toggle in the top right)
3. Go to **API Keys** → **Generate New Key**
4. Save your **API Key ID** and **Secret Key** (you won't see the secret again)

Alpaca gives you $100,000 in paper money automatically.

## 2. Create a GitHub Repository

1. Create a new **private** repository on GitHub (e.g., `spy-trading-bot`)
2. Install Git if you don't have it: [git-scm.com](https://git-scm.com)
3. Open a terminal in this project folder and run:
   ```
   git init
   git add .
   git commit -m "initial setup"
   git branch -M main
   git remote add origin https://github.com/YOUR_USERNAME/spy-trading-bot.git
   git push -u origin main
   ```

## 3. Install the Claude GitHub App

1. Go to [github.com/apps/claude](https://github.com/apps/claude)
2. Install it on your `spy-trading-bot` repository
3. Grant it permission to push to `main`

## 4. Create Scheduled Routines in Claude Code

Type `/schedule` in Claude Code to create routines.

Create **five routines** with these settings:

| Routine name | Cron (America/Chicago) | Command |
|-------------|------------------------|---------|
| spy-pre-market | `0 8 * * 1-5` | `/pre-market` |
| spy-market-open | `0 9 * * 1-5` | `/market-open` |
| spy-midday | `30 11 * * 1-5` | `/midday` |
| spy-daily-summary | `45 14 * * 1-5` | `/daily-summary` |
| spy-weekly-review | `0 15 * * 5` | `/weekly-review` |

**On each routine, set these environment variables directly** (not in a file):

| Variable | Value |
|----------|-------|
| `ALPACA_API_KEY` | your paper API key ID |
| `ALPACA_SECRET_KEY` | your paper secret key |

Point each routine at your `spy-trading-bot` GitHub repo, `main` branch.

## 5. Test with /portfolio

Run `/portfolio` manually in Claude Code. You should see:
- Your $100,000 paper balance
- No open positions
- Market status (open/closed)

If that works, you're ready to go.

## 6. Watch the Bot Run

The bot logs everything to the `memory/` files after each run. Monitor:
- `memory/RESEARCH-LOG.md` — what it analyzed pre-market
- `memory/TRADE-LOG.md` — every trade it places and why
- `memory/WEEKLY-REVIEW.md` — Friday performance summaries

## Adjusting the Strategy

Edit `memory/TRADING-STRATEGY.md` any time to change your rules.
The bot picks up the new rules on its next run automatically.

For major changes the bot suggests in the weekly review:
- It flags them in `WEEKLY-REVIEW.md` as "suggested adjustments"
- You review and approve by editing `TRADING-STRATEGY.md` yourself
