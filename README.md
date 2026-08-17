# Statistical arbitrage: AUD/NZD & WTI/Brent

A walk-forward pairs trading framework built across two structurally-linked pairs: AUD/NZD (commodity-bloc FX) and WTI/Brent (crude spread). Cointegration-tested pair selection, a Kalman filter for a causal time-varying hedge ratio, a Hidden Markov Model for regime detection, and a backtest with realistic transaction costs, volatility-targeted position sizing, and portfolio exposure limits.

The Kalman filter and HMM link to a Reinforced Deep Markov Model dissertation on regime-based portfolio control.

## Notebooks

Run in order, `00` through `06`. Each one loads its inputs from `data/` and saves its outputs there for the next.

- **00 - data acquisition**: pulls AUD/USD, NZD/USD, WTI, and Brent from yfinance, cleans them, fixes a holdout split (2024-08-15 onward) that stays untouched until the last notebook.
- **01 - pair selection**: Engle-Granger and Johansen cointegration tests. AUD/NZD fails on the full sample due to a structural break around 2013, but is cointegrated within the post-2014 regime.
- **02 - Kalman filter**: causal, time-varying hedge ratio, implemented from scratch rather than via a library. Compared against static and rolling-window OLS.
- **03 - regime detection**: walk-forward Gaussian HMM, filtered (not smoothed) so it stays causal. Classifies the spread into calm/volatile regimes to gate trade entries.
- **04 - signal generation**: z-score entry/exit rules with a stop-loss and max holding period.
- **05 - backtest engine**: turns signals into P&L - execution timing, transaction costs, volatility-targeted and fractional-Kelly position sizing, portfolio-level exposure caps.
- **06 - ablations & holdout**: Kalman vs rolling-OLS, HMM on/off, cost sensitivity, and the final evaluation on the untouched holdout period.

## Results

Net of costs, volatility-targeted sizing:

| | train Sharpe | holdout Sharpe | holdout return |
|---|---|---|---|
| AUD/NZD | -0.51 | -0.69 | -3.4% |
| WTI/Brent | 0.92 | 1.08 | +17.2% |
| Combined (50/50) | 0.56 | 0.81 | +6.6% |

The holdout period (2024-08-15 onward) was never used to choose any parameter - hedge ratio, regime model, signal thresholds, sizing, costs are all frozen before that date.

AUD/NZD loses money after realistic costs, in-sample and out-of-sample. It's genuinely cointegrated within its post-2014 regime, but a daily-frequency mean-reversion signal doesn't clear costs on a G10 FX cross at this frequency. WTI/Brent's edge holds up on the holdout.

Ablations: swapping the Kalman filter for a rolling-OLS hedge ratio turns WTI/Brent from Sharpe 0.92 to -0.22 - the dynamic hedge ratio is doing real work there. The HMM regime filter is closer to a wash (helps AUD/NZD slightly, hurts WTI/Brent slightly), so it's not a clear net positive despite being correctly implemented.

## Data

Daily closes from yfinance (`AUDUSD=X`, `NZDUSD=X`, `CL=F`, `BZ=F`). No intraday data or measured bid-ask spreads - transaction costs are assumptions, sensitivity-checked in notebook 06. WTI traded at -$37.63/bbl on 2020-04-20; handled explicitly (simple returns instead of log returns, spread as a difference not a ratio).

## Limitations

Two pairs only. The HMM regime filter doesn't clearly earn its complexity on this data. ~2 years of holdout data is enough for a genuine out-of-sample test but not enough to be statistically conclusive on its own. Costs and sizing use flat assumptions rather than measured execution data.
