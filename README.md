# Short-Horizon PM2.5 Forecasting at Marine Terrace, Singapore

Predictive Analytics group project (theme: Energy and Environment). Time-series forecasting of hourly PM2.5 at one low-cost sensor (OpenAQ location 6301631), April to August 2026, at **1, 3 and 6 hours ahead**.
Question: do wind direction and lagged daily weather improve forecasts beyond PM2.5 history alone?
This is a single-location experiment, **not** a validated haze early-warning system.

## Run it
Local: `python -m venv .venv`, activate it, `pip install -r requirements.txt`, then open `notebooks/01_pm25_short_horizon_forecasting.ipynb` and use **Restart and Run All** (about 3 minutes).
Colab: upload the notebook, run it, and use the upload dialog in Section 3 to add the 15 CSV files from `data/` (they are stored in a `data/` folder created next to the notebook).

## Layout
- `data/` the exact 15 CSV files used (5 OpenAQ, 5 daily weather, 5 wind direction); never modified
- `notebooks/01_pm25_short_horizon_forecasting.ipynb` complete analysis with outputs
- `outputs/figures` figures; `outputs/tables` audit, split, ablation, feature dictionary, findings; `outputs/metrics` model, tuning and bootstrap results
- `docs/methodology.md` methods; `docs/slides_outline.md` presentation outline; `contribution_statement.md`

## Method in brief
Observed PM2.5 on a complete hourly grid (missing hours stay missing, targets never filled); features from information available at the forecast origin (wind from the last completed hour, daily weather from the previous day or earlier); chronological 70/15/15 split by target time with purging; baselines (persistence, seasonal naive); candidates Random Forest, HistGradientBoosting, ElasticNet; two shortlisted models tuned by grid search with four expanding-window folds on train + validation; final model chosen by cross-validation before the test period; 24 h moving-block bootstrap intervals.

## Main results (test period 13 to 31 Aug 2026)
| Horizon | Final model (chosen by CV) | Test MAE | Persistence MAE | MAE skill (approx. 95 % interval) |
|---|---|---|---|---|
| 1 h | ElasticNet, tuned | 5.22 | 5.31 | +1.6 % (+0.8 to +2.2 %) |
| 3 h | ElasticNet, tuned | 8.28 | 8.52 | +2.8 % (+1.9 to +3.4 %) |
| 6 h | ElasticNet, tuned | 8.84 | 10.97 | +19.5 % (+11.9 to +26.8 %) |

- Persistence is very hard to beat at 1 and 3 h; only the 6 h gain is substantial.
- Tuning improves cross-validation error but not reliably the test error (at 3 h the untuned ElasticNet scored 7.69 on test, better than the tuned model).
- Wind direction and lagged weather do not meet the evidence rule in the ablation.
- Errors are roughly 5 to 8 times larger on hours above 55 µg/m3, and there are only about 16 such test hours.
- Train, validation and test levels differ (mean 17.5, 27.8, 35.4 µg/m3), so test numbers are sensitive to drift.

## Limitations
One low-cost sensor; calibration and humidity effects; the data do not cover the October haze episode; long outages (17 days in April/May); coarse daily weather from another station; wind station unknown; no hourly wind speed, fire or satellite data; few extreme events; autocorrelation shrinks the effective sample size; not a 24 to 48 hour warning system.
