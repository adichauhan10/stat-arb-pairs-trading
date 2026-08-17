# Pair selection report

## WTI / Brent
- Engle-Granger p-value (full train sample): 0.0060
- ADF on static-OLS spread residual: 0.0011
- Johansen trace stat: 43.75 (95% crit: 15.49)
- Rolling 756-day window: 38.6% significant at 5%
- Verdict: cointegrated. Proceed using full effective history from 2007-07-30.

## AUD / NZD
- Engle-Granger p-value (full train sample): 0.2839 - not significant
- ADF on static-OLS spread residual: 0.1182 - not significant
- Johansen trace stat: 15.00 (95% crit: 15.49) - fails at 95%
- Rolling 756-day window: 1.0% significant at 5%
- Post-2014 sub-sample Engle-Granger p-value: 0.00046 - strongly significant
- Verdict: cointegrated within regime. The full-sample failure is driven by a level shift
  around 2013 (see `00_raw_series_overview.png` / `01_audnzd_regime_split.png`), not an absent
  relationship. AUD/NZD analysis in notebooks 02-05 is restricted to data from 2014-01-01
  onward, with the Kalman-filter hedge ratio and HMM regime filter treated as required for
  this pair rather than optional extras.

Decisions saved to `pair_selection_decisions.json` for notebooks 02+ to load.
