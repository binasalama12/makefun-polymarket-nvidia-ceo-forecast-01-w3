# polymarket-nvidia-ceo-forecast-01 entry — Stork (w3)

A forecaster for Polymarket's "Next CEO of NVIDIA?" event. `main.py`
(Python 3.13, standard library only) reads one case from the input
directory and writes, for each market and each of the 240 minutes
after the checkpoint, five quantiles (5%, 25%, 50%, 75%, 95%) of
that minute's price to the output directory.

## Model

The lean, robust variant of the family:

- **Median = persistence.** The last observed price.
- **Short memory:** volatility and the empirical change table come
  from the last 720 minutes only.
- **Median-based sigma:** 1.4826 × the median absolute one-minute
  change (floor 0.0008, cap 0.003), so one wild print barely moves
  the estimate — unlike a mean.
- **Spread = a 35/65 blend** leaning Gaussian, since the empirical
  h-step quantiles get noisy on a 12-hour window: quantiles of
  h-minute changes at anchors 1, 5, 15, 30, 60, 120, 240,
  interpolated in sqrt(h), blended with the Gaussian walk.
- **Volatility regime:** last 6 hours vs the whole history,
  clamped to [0.8, 1.4].

Quantiles are clamped to [0, 1], kept non-decreasing and rounded to
6 decimals. Deterministic; well under a second per case.

Backtested on the kit's practice inputs (later cases as ground
truth), this scores a mean pinball loss of about 0.362 against the
house baseline's 0.399 on the same folds.

## License

MIT — see `LICENSE`.
