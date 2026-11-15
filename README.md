# BTC Race Bot — breakout momentum (variant 1)

A long-only bot for make.fun's "Race a BTC trading bot live for 24 hours"
(`btc-race-24h-20261115-0828`). It trades Coinbase Wrapped BTC (cbBTC) vs
USDC on 1-minute bars, starting with $1,000 of paper money.

## Strategy

In a noisy 1-minute market a full round trip costs ~40 bps (4 bps fee +
2 bps slippage + ~15 bps of constant-product impact each way at full size),
so the bot only trades when a LARGE move is already underway:

- **Enter** when the trailing `W_BARS`-bar return clears `UP_BPS`
  (a bounce or a fresh up-leg, confirmed — not a guess at the bottom).
- **Exit** on a tight trailing stop (`TRAIL_BPS` below the peak close since
  entry), a hard stop (`STOP_BPS` below the entry close), or a momentum
  reversal (the `W_BARS`-bar return falls to `DOWN_BPS`).
- It stays in cash through the chop. Cash earns nothing, but it also loses
  nothing — which is how it survives a falling window.

All tunable values are constants at the top of `main.ts` (basis points
unless noted), so a variant is a one-line change. The bot is deterministic:
integer (`BigInt`) math only, no clock, no environment, no `Math.random`.

## Parameters (this variant)

| Parameter | Value |
|---|---|
| `W_BARS` | 8 |
| `UP_BPS` | 120 (+1.20%) |
| `DOWN_BPS` | -80 (-0.80%) |
| `TRAIL_BPS` | 80 (0.80%) |
| `STOP_BPS` | 200 (2.00%) |
| `TAKE_PROFIT_BPS` | 0 (disabled) |
| `SIZE_BPS` | 10000 (all-in) |

## Run it

```sh
node main.ts
```

The bot speaks `agent-fun/bars-replay/v1` on stdin/stdout (see
`vendor/makefun-bars.ts`). It is graded with the kit's `grade-local`
command. Node 22, standard library only, no network.

## Dev-window result

- Net PnL: **+$21.68** on the 24 h dev window (1 round trip)
- Trades: 2 (1 buy + 1 sell), max drawdown 1.09%, fees $0.81
- Best baseline on the same window: momentum, -$40.92

## License

MIT — see `LICENSE`.
