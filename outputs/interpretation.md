**1. Does any model beat persistence on validation MAE?**
- 1 h: persistence 3.10; RandomForest 3.49, HistGB 3.51, ElasticNet 3.48. None beats persistence.
- 3 h: persistence 5.48; RandomForest 5.58, HistGB 5.47, ElasticNet 5.48. Better than persistence: HistGB, ElasticNet.
- 6 h: persistence 7.19; RandomForest 7.34, HistGB 7.24, ElasticNet 6.76. Better than persistence: ElasticNet.

**2. Does the improvement persist on the test period?** (final model per horizon, chosen by cross-validation on train + validation, then scored once on test)
- 1 h: ElasticNet tuned test MAE 5.22 vs persistence 5.31; it improves on persistence, skill +1.6% (95 % block-bootstrap interval +0.8% to +2.2%).
- 3 h: ElasticNet tuned test MAE 8.28 vs persistence 8.52; it improves on persistence, skill +2.8% (95 % block-bootstrap interval +1.9% to +3.4%).
- 6 h: ElasticNet tuned test MAE 8.84 vs persistence 10.97; it improves on persistence, skill +19.5% (95 % block-bootstrap interval +11.9% to +26.8%).

**3. How does performance change from 1 to 6 hours ahead?**
- Test MAE, persistence: 5.31 -> 8.52 -> 10.97 µg/m³ for 1, 3, 6 h; final model: 5.22 -> 8.28 -> 8.84; final-model R²: 0.28 -> -0.20 -> -0.12. Errors grow with the horizon because the most recent value says less about the future.

**4. Do wind and lagged weather improve performance?**
- 1 h (ElasticNet): FS1 test MAE 5.25; + wind direction: test change +0.11, interval +0.06 to +0.15; validation change +0.05; + lagged daily weather: test change +0.14, interval +0.05 to +0.22; validation change +0.06 (MAE change vs FS1; negative = better).
- 3 h (HistGB): FS1 test MAE 9.17; + wind direction: test change +0.05, interval -0.02 to +0.12; validation change +0.01; + lagged daily weather: test change +0.02, interval -0.19 to +0.25; validation change -0.05 (MAE change vs FS1; negative = better).
- 6 h (ElasticNet): FS1 test MAE 9.06; + wind direction: test change +0.12, interval +0.05 to +0.20; validation change +0.09; + lagged daily weather: test change +0.17, interval +0.01 to +0.28; validation change -0.18 (MAE change vs FS1; negative = better).
- A gain counts as evidence only if it is negative on validation *and* test and its interval excludes zero; otherwise the features are not shown to help in this dataset.
  - 1 h, + wind direction: does not meet the evidence rule.
  - 1 h, + lagged daily weather: does not meet the evidence rule.
  - 3 h, + wind direction: does not meet the evidence rule.
  - 3 h, + lagged daily weather: does not meet the evidence rule.
  - 6 h, + wind direction: does not meet the evidence rule.
  - 6 h, + lagged daily weather: does not meet the evidence rule.

**5. Are the train, validation and test distributions different?**
- Mean / median / 95th pct / max (1 h targets): train: 17.5 / 14.7 / 38.1 / 293; val: 27.8 / 27.1 / 42.7 / 87; test: 35.4 / 34.6 / 50.9 / 131.
- Kolmogorov-Smirnov tests: train vs val D = 0.46; train vs test D = 0.62 (p-values are not reliable for autocorrelated data; D is shown as an effect size). Larger D means a stronger shift in level, which makes the level target harder to predict out of period.

**6. How much uncertainty is associated with the evaluation?**
- 1 h: model test MAE 5.25 (95 % interval 4.19 to 6.50, n = 421).
- 3 h: model test MAE 9.17 (95 % interval 7.67 to 10.78, n = 419).
- 6 h: model test MAE 9.06 (95 % interval 7.45 to 10.92, n = 416).
- The test period is a single contiguous stretch with few independent episodes, so these intervals are approximate and probably optimistic.

**7. Does the model perform poorly during high-PM2.5 episodes?**
- 1 h: 16 test hours above 55 µg/m³ in 11 episode(s); model MAE 33.6 there vs 4.1 on the other hours; persistence MAE 31.6 on those hours.
- 3 h: 16 test hours above 55 µg/m³ in 11 episode(s); model MAE 38.6 there vs 8.0 on the other hours; persistence MAE 34.4 on those hours.
- 6 h: 15 test hours above 55 µg/m³ in 10 episode(s); model MAE 41.7 there vs 7.8 on the other hours; persistence MAE 36.1 on those hours.
- With so few high hours and episodes, this analysis cannot show that the model detects spikes reliably, and no such claim is made.

**Bottom line.** Read the numbers above first: if persistence is as good as or better than the learned models, that is a valid result, not a failure. This notebook tests short-horizon forecasting at one sensor; it does not validate a 24 to 48 hour haze-warning system.