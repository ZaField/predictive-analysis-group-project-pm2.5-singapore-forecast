# Methodology

## Data
- OpenAQ PM2.5 (`parameter == "pm25"`), 5 monthly files; UTC timestamps converted to Asia/Singapore; the local-time column is used only as a cross-check.
- Daily weather (Marina Barrage, S108): rainfall and mean/max wind speed; temperature columns are 100 % empty and unused.
- Hourly wind direction: `timestamp`, `observation_count`, `wind_direction_deg`; hours with fewer than 30 readings set to missing.
- Date ranges are derived programmatically; only April to August 2026 are supplied.

## Preparation
- Duplicate timestamps: median. Complete hourly grid; missing hours stay missing.
- Exact zeros (24) are treated as missing by default (isolated, next to normal readings); sensitivity re-run with zeros kept (notebook Section 13b). No high values are removed.
- The 407 h outage (28 Apr 23:00 to 15 May 21:00) is documented; three 24 h gaps at month boundaries look like export-window artifacts.

## Leakage controls
- Features use data up to the forecast origin only; PM2.5 inputs forward-filled at most 6 h (flagged; at most 6 filled hours in the last 24 h); targets are never filled.
- Wind stamped H is usable from H+1 h. Daily weather for day D comes from D-1 or earlier.
- Chronological 70/15/15 split by target time; samples whose origin and target fall in different periods are purged; preprocessing is fitted on the training rows only.
- A perturbation test (replace all future values) confirms earlier features are unchanged.

## Experiment
- Baselines: persistence, seasonal naive (same hour previous day).
- Candidates: RandomForest, HistGradientBoosting, ElasticNet; target as level or as change from persistence (mode chosen on validation only). The two strongest by mean validation MAE are shortlisted for tuning.
- Initial comparison: fit on train, scored on validation; the primary model per horizon used in the feature ablation is the lowest validation MAE on FS1. Ablation FS1 (history + time), FS2 (+ wind), FS3 (+ lagged weather) on identical rows.
- Metrics: MAE, RMSE, R², n, target range, MAE skill = 1 - MAE_model / MAE_baseline; 24 h moving-block bootstrap (2000 replicates), approximate because of autocorrelation and few independent episodes.

## Hyperparameter tuning and final selection
- Development set = train + validation; test excluded. Four expanding-window folds by target time, boundary-crossing rows purged.
- Exhaustive grid search, metric = mean CV MAE: ElasticNet alpha x l1_ratio (12 configurations), HistGB learning_rate x max_depth x min_samples_leaf (18), Random Forest min_samples_leaf x max_features (12, only if shortlisted). The default configuration is in every grid.
- Final rule, fixed before the test period: the lowest CV MAE among default and tuned versions of the shortlisted models; persistence if none beats it in CV. The final model is refit on the development set and scored once on test.

## Limitations
See README.
