# Marketing Uplift Modeling (Criteo dataset)

**Question:** Which users should we show the ad to, so that the ad actually causes extra conversions?

Uplift = conversion rate of users who saw the ad − conversion rate of users who did not.

**Data:** Criteo Uplift dataset (search "Criteo uplift" on Kaggle) · 13.98M users · 12 anonymous features · ~85% treated / 15% control · conversion ~0.29%.
Download it and put the CSV in a `data/` folder (not included here).

## Flow

1. **EDA:** nulls, correlations, group balance, feature structure.
2. **Z-test:** does the ad work at all?
3. **Split:** train / validation / test (60 / 15 / 25), stratified on treatment × conversion.
4. **Models (XGBoost via** `causalml`**):** T-learner (T-v1) and X-learner (X-v1, X-v2).
5. **Evaluate:** Qini score, Qini curves, decile tables with confidence intervals, bootstrap comparison.
6. **Business cutoff:** how many users to target.



## Results

**1. The ad works, but the effect is small**

- Conversion: treated users convert **+0.115 pp** more than control (95% CI 0.108 to 0.122).
- Visit: **+1.034 pp** (95% CI 1.005 to 1.063).
- p < 0.001 for both.

![z-test](images/ztest.png)

**2. Groups are balanced**

- Largest standardized difference between treated and control is 0.049, so the comparison is fair.

**3. Models find the right users**

- Qini score on test: T-v1 **0.38**, X-v1 **0.35**, X-v2 **0.39**.
- The top 10% of users ranked by predicted uplift really have about **+0.8 pp** extra conversions.

![qini](images/qini_curves.png)
![deciles](images/decile_uplift_ci.png)

**4. Model comparison**

- Bootstrap (500 resamples): X-v2 is slightly better than X-v1.
- No clear winner between X-v2 and T-v1.
- X-v2 is the most stable, so it is used for the cutoff.

**5. Who to target (X-v2, test set)**


| Targeted | Users   | Incremental conversions | Share of total benefit |
| -------- | ------- | ----------------------- | ---------------------- |
| Top 10%  | 349,489 | 2,452                   | 72%                    |
| Top 20%  | 698,979 | 2,827                   | 83%                    |


- Each further 10% adds only about 170–370 conversions.
- **Start with the top 20%.** The final cutoff depends on the ad cost and the value of a conversion, which are not in the data.



## Run it

- Python, `pandas`, `statsmodels`, `matplotlib`, `xgboost`, `causalml`
- On Mac: `brew install libomp` (needed by XGBoost), then restart the kernel.
- Open `marketing_uplift_modelling.ipynb` and run all cells.

