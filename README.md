# BTC 4-hour forecast: persistence with a light median denoise

Entry for make.fun `btc-cent-forecast-01` — forecast Bitcoin's US-dollar price
240 minutes (4 hours) after each checkpoint, from the 4,320 one-minute closes
of the 72 hours up to it.

## Model

At a 4-hour horizon BTC is a near-random walk: trend extrapolation and
mean-reversion both lose against simply guessing the last close, and denoising
the last few closes is worth about ±1%. Pure persistence ties the house
baseline and can never win a prize (a prize needs a score strictly below it),
so the forecast is mostly the checkpoint close, pulled 25% toward the median
of the last 5 closes to shave off a noisy last tick:

```
forecast = 0.75 * last + 0.25 * median(last 5 closes)
```

clamped to (0.00001, 9,999,999.99999) and rounded to 5 decimals.

## Practice results

Practice MAE ~44,167 cents vs the persistence baseline's 44,521.58 cents.
Honest assessment: a lottery ticket around the baseline — roughly a coin flip
to beat it on the final. No tested model robustly beats persistence at this
horizon.

## Run

```
MAKEFUN_INPUT_DIR=<input dir> MAKEFUN_OUTPUT_DIR=<output dir> node main.ts
```

Node 22, standard library only, no network. Reads every `*.in` file in the
input dir and writes one `/output/<id>.out` per case:

```json
{"forecasts":[{"case":"p00","price":74668.48}]}
```

## License

MIT — see `LICENSE`.
