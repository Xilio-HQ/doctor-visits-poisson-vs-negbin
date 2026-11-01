# Predicting Annual Doctor Visits: Poisson vs Negative Binomial Regression

Mini project for CSE3141 Predictive Analytics (Manipal University Jaipur).

## Problem statement

A health insurer wants to estimate how many times a person visits a doctor in a year from their profile, so it can plan costs. The target (number of visits) is a count with many zeros and a long right tail: 79.79% of people made no visit at all.

The project fits two regression models from the course on the same dataset, Poisson Regression and Negative Binomial Regression, and compares them using evidence from the data.

## Dataset

- **DoctorVisits** from the `AER` R package, distributed in the Rdatasets collection.
- Source: https://vincentarelbundock.github.io/Rdatasets/csv/AER/DoctorVisits.csv
- 5,190 rows, 11 predictors, no missing values. A copy is included as `DoctorVisits.csv`.
- Target: `visits` (number of doctor visits). Predictors: gender, age, income, illness, reduced, health, private, freepoor, freerepat, nchronic, lchronic.

## How to run

```
pip install -r requirements.txt
jupyter notebook analysis.ipynb
```

Run all cells from the top. The notebook reads `DoctorVisits.csv` from the same folder and falls back to the URL above if the file is missing.

## Methods

- Exploratory data analysis: distribution of the target, box plots and IQR outlier counts, correlation heatmap, visits against illness and age.
- Overdispersion check (variance against mean) and Pearson dispersion of the fitted Poisson model.
- Variance inflation factors to check multicollinearity.
- 80/20 train-test split (`random_state=42`) and 5-fold cross-validation.
- Poisson regression (`statsmodels` GLM) and Negative Binomial regression (`statsmodels` NegativeBinomial).
- Comparison with log-likelihood, AIC, BIC, RMSE, MAE, and the predicted share of zeros.
- Incidence rate ratios from the Negative Binomial model.

## Results

| Model | Log-likelihood | AIC | BIC | Test RMSE | Test MAE | CV RMSE (mean) | CV MAE (mean) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Poisson | -2612.08 | 5248.16 | 5324.14 | 0.8612 | 0.4385 | 0.7407 | 0.4190 |
| Negative Binomial | -2506.89 | 5039.78 | 5122.08 | 0.8769 | 0.4427 | 0.7881 | 0.4277 |

Other key numbers:

- Variance of visits divided by mean: 2.11. Poisson Pearson chi-square divided by residual degrees of freedom: 1.28.
- Estimated Negative Binomial alpha: 1.04.
- Share of zeros on the test set: actual 77.84%, Poisson 77.79%, Negative Binomial 80.63%.

## Conclusion

Negative Binomial fits the data better by likelihood: its AIC is 208.4 lower than Poisson, its BIC is lower, and alpha is clearly above zero. The reason is overdispersion that Poisson cannot represent, which also makes Poisson standard errors too small (it flagged private insurance and a non-limiting chronic condition as significant when Negative Binomial does not).

However, Negative Binomial did not predict better. Poisson had slightly lower test and cross-validated RMSE, and it matched the test share of zeros more closely. The advantage of Negative Binomial is in the distribution and the inference, not in point prediction.

Limits: one survey from one country and period, self-reported predictors, a small test set, and only two models. A zero-inflated or hurdle model may fit better given the large share of zeros.
