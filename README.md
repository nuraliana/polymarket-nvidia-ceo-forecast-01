# NVIDIA CEO price forecaster

Forecasts the Yes price of every trading market of Polymarket's
"Next CEO of NVIDIA?" event at every minute of the 4 hours after a
checkpoint, as five quantiles (0.05, 0.25, 0.5, 0.75, 0.95).

Entry for the make.fun bounty
[Forecast Polymarket's next NVIDIA CEO prices minute by minute, 4 hours ahead](https://make.fun/bounty/polymarket-nvidia-ceo-forecast-01).

## How it works

Python 3, standard library only, no network, deterministic. For each
market, from the case's own 72 hours of 1-minute prices:

- **Centre:** the last price (a martingale — smoothed centres scored worse
  on the practice cases).
- **Drift:** a gentle pull toward the market's normalised share (the 24 Yes
  prices are mutually exclusive chances, so they should sum to ~1), plus a
  small reversal of the last 4-hour move.
- **Spread:** a random-walk widening, `sigma * sqrt(h)`, where sigma is the
  market's own recent volatility — the RMS of its 1-minute moves over the
  last 120 minutes, winsorised at 0.01, floored at 0.0004. Quiet markets
  get tight intervals; jumpy markets get wide ones.

Quantiles are the normal quantiles around the drifted centre, clamped to
[0, 1] and rounded to 6 decimals.

## Run it locally

```sh
mkdir -p out
MAKEFUN_INPUT_DIR=<kit>/input MAKEFUN_OUTPUT_DIR=out python3 main.py
```

It reads every `<id>.in` in the input directory and writes `<id>.out`
(`{"forecasts": [{"id", "q": [[5 numbers] x 240]}]}`) for each.

## Local backtest

Practice cases p00–p04, scored against the next case's prices (the kit's
own shape): mean pinball loss **0.307**, vs the house baseline's **0.398**
on the same cases (the baseline's published 6-case practice loss is 0.364).
It beats the baseline on every one of the five cases. Runtime ~0.1 s per
case (limit 8 s); two runs are byte-identical.

## License

MIT — see [LICENSE](LICENSE).

---

Optional: earn referral rewards — [make.fun](https://make.fun/r/ctnjhg8e)
