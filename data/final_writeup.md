# Statistical arbitrage: AUD/NZD & WTI/Brent

## Methodology
Cointegration-tested pair selection (Engle-Granger + Johansen, notebook 01), causal Kalman-filter
dynamic hedge ratio (notebook 02), walk-forward HMM regime filter (notebook 03), z-score signal with
stop-loss/max-holding risk controls (notebook 04), backtest with transaction costs, volatility-targeted
sizing and portfolio exposure caps (notebook 05), ablations and a frozen-parameter holdout evaluation
on data never used during development (notebook 06).

## Headline results (net of costs, vol-targeted sizing)
| pair              |   train_sharpe |   holdout_sharpe |   train_days |   holdout_days |   holdout_cum_return |   holdout_max_drawdown |
|:------------------|---------------:|-----------------:|-------------:|---------------:|---------------------:|-----------------------:|
| AUD/NZD           |      -0.510974 |        -0.692564 |         2427 |            516 |           -0.0344969 |             -0.0623699 |
| WTI/BRENT         |       0.920637 |         1.07929  |         3895 |            502 |            0.171678  |             -0.0396863 |
| Portfolio (50/50) |       0.564767 |         0.805455 |         3983 |            517 |            0.0657788 |             -0.038091  |

## Findings
- WTI/Brent: cointegrated, profitable after costs in-sample (Sharpe 0.92) and
  out-of-sample on a 502-day untouched holdout
  (Sharpe 1.08).
- AUD/NZD: cointegrated within its post-2013 regime, but doesn't clear transaction costs at this
  signal frequency, in-sample or out-of-sample.
- Kalman vs rolling-OLS hedge ratio: replacing the Kalman filter with a rolling-OLS beta turns
  WTI/Brent from profitable to unprofitable.
- HMM regime filter: correctly implemented and walk-forward, mixed-to-negative value-add in the
  ablation.
- Blending both pairs 50/50 reduces max drawdown versus either sleeve alone, even though AUD/NZD
  is a net drag on blended return.

See `data_quality_notes.md`, `pair_selection_report.md`, `ablation_summary.csv`, and
`holdout_metrics.csv` for the detail behind each of these.
