# Presentation outline (12 minutes, 10 to 12 slides, business pitch to a C-level panel)

1. Title and decision context: who needs a short PM2.5 outlook and why
2. The question and the stakeholder (estate operations lead; NEA analyst secondary)
3. Data in one slide: three sources, five months, one sensor; quality issues (17-day outage, zeros)
4. How we kept the test honest: chronological split, no leakage, baselines, tuning on train + validation only
5. Result 1: persistence is the benchmark; tuned model vs persistence at 1, 3, 6 h (Figure 12 / table)
6. Result 2: the 6 h forecast improves MAE by about 20 % (with interval)
7. Result 3: wind and lagged weather add little (ablation figure)
8. Where it fails: high-PM2.5 hours and drift (Figure 7, error on high hours)
9. Recommendations (operating rule, data investment, scope guard)
10. Implementation, risks and next steps (retraining, data freshness, haze season)
11. Limitations in one slide
12. Backup: tuning search space and CV results; questions from the stakeholder panel

Anticipated panel questions: Is this reliable enough to act on? What does it cost? What if a haze event occurs? Why not a more complex model? Who maintains it?
