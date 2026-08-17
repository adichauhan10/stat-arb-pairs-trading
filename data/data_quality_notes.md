# Data quality notes

## Sources
- FX: yfinance `AUDUSD=X`, `NZDUSD=X` (effective common start: 2006-05-16)
- Commodities: yfinance `CL=F` (WTI), `BZ=F` (Brent) continuous futures (effective common start: 2007-07-30)

Tickers within a pair start on different dates in yfinance, so the effective start above
(after the inner join) is later than either individual ticker's raw history.

## WTI negative price, 2020-04-20
WTI (CL=F) closed at -37.63 USD/bbl on 2020-04-20 - the COVID demand collapse / Cushing storage
crunch. Brent didn't go negative, so this is a real dislocation between the two legs, not a data
error. Return calcs use simple returns rather than log returns because of this (log is undefined
for non-positive prices), and the spread is a difference rather than a ratio for the same reason.

Worth checking in notebook 01 whether this date coincides with any instability in the rolling
cointegration test.

## Jump days (|z| > 6 on simple returns)
- AUD: [datetime.date(2008, 10, 6), datetime.date(2008, 10, 8), datetime.date(2008, 10, 10), datetime.date(2008, 10, 13), datetime.date(2008, 10, 15), datetime.date(2008, 10, 24), datetime.date(2008, 10, 28), datetime.date(2025, 4, 7)]
- NZD: [datetime.date(2008, 10, 24), datetime.date(2008, 10, 28)]
- WTI: [datetime.date(2020, 4, 20), datetime.date(2020, 4, 21), datetime.date(2020, 4, 22)]
- BRENT: [datetime.date(2019, 9, 16), datetime.date(2020, 3, 9), datetime.date(2020, 4, 2), datetime.date(2020, 4, 21), datetime.date(2026, 4, 1)]

## Missing business days
- FX: 17 / 5283
- Commodities: 231 / 4969

Commodities have a higher gap rate, consistent with CME holiday closures not lining up with FX's
near-continuous trading. Rolling-window notebooks should check the two legs are aligned before
computing spreads.

## Train / holdout split
- Train: start -> 2024-08-15
- Holdout: 2024-08-15 -> 2026-08-14 (untouched until notebook 06)
