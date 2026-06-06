# Portfolio — Ad-Hoc Command

Get a live snapshot of the paper account and current SPY position.

## Steps

```
GET https://paper-api.alpaca.markets/v2/account
GET https://paper-api.alpaca.markets/v2/positions/SPY
GET https://paper-api.alpaca.markets/v2/orders?status=open&symbols=SPY
GET https://paper-api.alpaca.markets/v2/clock
GET https://data.alpaca.markets/v2/stocks/SPY/bars?timeframe=1Min&limit=1&feed=iex
```

Format and display:

```
=== PAPER ACCOUNT SNAPSHOT ===
Time: {HH:MM CT} | Market: OPEN / CLOSED

Account
  Portfolio Value:  $XX,XXX.XX
  Cash:             $XX,XXX.XX
  Buying Power:     $XX,XXX.XX

SPY Position
  {LONG/SHORT} XX shares | Entry: $XXX.XX | Now: $XXX.XX
  Unrealized P&L: $XX.XX ({direction})
  [OR: No open SPY position]

Open Orders
  {STOP/LIMIT} {SIDE} XX shares SPY @ $XXX.XX
  [OR: No open orders]

Today's closed P&L: $XX.XX
```
