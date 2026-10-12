# Notebook 4 — Final result values for thesis

**Official test**

Run UTC: 2026-10-12T10:52:31.025976+00:00. Evaluated rows: 168,060; benign: 91,260; phishing: 76,800; observed prevalence: 0.456980.

Train and test share benign calendar date 2025-09-08; not globally strictly temporally separated.

All classifications use frozen estimator.predict(); no refit, tuning, calibration or threshold change.
AP is sklearn average_precision_score. Precision, recall and F1 use phishing=1; zero-division classification metrics are zero.

## Frozen-model results

| Model | AP [95% CI] | F1 [95% CI] | MCC [95% CI] | FPR | P@R≥.90 (achieved recall) |
|---|---|---|---|---|---|
| M0: Majority | 0.456980 [0.454575, 0.459357] | 0.000000 [0.000000, 0.000000] | 0.000000 [0.000000, 0.000000] | 0.000000 | 0.456980 (1.000000) |
| M1: LR URL | 0.882938 [0.881181, 0.884627] | 0.793949 [0.792033, 0.796200] | 0.626021 [0.622695, 0.629927] | 0.156936 | 0.680476 (0.900000) |
| M2: LR URL+HTML | 0.932354 [0.931108, 0.933592] | 0.845743 [0.843820, 0.847489] | 0.719547 [0.715940, 0.722539] | 0.116130 | 0.798254 (0.900000) |
| M3: RF URL | 0.954911 [0.953985, 0.955792] | 0.860091 [0.858269, 0.861838] | 0.751807 [0.748631, 0.754806] | 0.083410 | 0.836324 (0.900039) |
| M4: RF URL+HTML | 0.978016 [0.977313, 0.978728] | 0.913092 [0.911541, 0.914663] | 0.851013 [0.848641, 0.853554] | 0.025510 | 0.946111 (0.900000) |

M0 predicts benign for every row: AP equals prevalence and ROC-AUC is 0.5. Its zero class-1 score is expected.
P@R=.90 uses the highest observed threshold attaining target recall, keeping all tied scores. This descriptive operating point is NOT a deployment threshold.

## Paired differences and inference

- M2-M1, F1: difference +0.051795, 95% paired percentile CI [+0.049851, +0.053612].
- M2-M1, MCC: difference +0.093526, 95% paired percentile CI [+0.090091, +0.096705].
- M2-M1, AP: difference +0.049417, 95% paired percentile CI [+0.048311, +0.050720].
- M4-M3, F1: difference +0.053001, 95% paired percentile CI [+0.051415, +0.054589].
- M4-M3, MCC: difference +0.099206, 95% paired percentile CI [+0.096521, +0.101915].
- M4-M3, AP: difference +0.023105, 95% paired percentile CI [+0.022394, +0.023845].
- McNemar M2-M1: URL correct/hybrid wrong=8,751; URL wrong/hybrid correct=16,527; p=0; asymptotic continuity-corrected; hybrid fewer errors; significant at unadjusted alpha=.05; two predeclared comparisons.
- McNemar M4-M3: URL correct/hybrid wrong=3,643; URL wrong/hybrid correct=11,710; p=0; asymptotic continuity-corrected; hybrid fewer errors; significant at unadjusted alpha=.05; two predeclared comparisons.
- M2-M1 FPR difference: -4.0806 percentage points; negative is desirable.
- M4-M3 FPR difference: -5.7901 percentage points; negative is desirable.

Bootstrap: 500 successful paired samples of n rows with replacement, seed 42, 0 one-class samples rejected. Each row's multiplicity is shared across all models. CIs are not p-values; AP improvement alone does not establish McNemar significance.
Two predeclared McNemar tests use unadjusted alpha=.05 (no familywise multiplicity adjustment). Row bootstrap assumes independent rows; related-domain dependence may narrow intervals. Test prevalence differs from development validation prevalence 28434/100775 ≈ 0.282153; AP depends on prevalence. No rebalancing applied.

## Interpretation and efficiency

M2 coefficients are signed effects on phishing log-odds per training-standardized feature unit, conditional on other included predictors; correlations affect magnitude/sign. They are associations, not causal effects. Saved imputation/scaling remain frozen.
M4 permutation importance used 10,000 uniform random test rows without replacement (seed 42, fixed before scores), sample prevalence=0.461800, AP scoring, five repeats. Mean AP drop and SD are subset-dependent descriptive values; correlated predictors may share importance. No feature reselection follows.
Efficiency includes saved artifact sizes plus one timed full predict_proba and predict call after consistent 128-row warmup. Latency is normalized to 1,000 rows. Frame construction/export is excluded. Values depend on hardware/threading; RSS snapshots are not peak RAM. NB4 does not measure training time.

## Published comparison and limitations

Same-test published AP and P@R=.90 values were not supplied or verified. Context CSV marks them unavailable and F30 plots only this project's measured results. No direct superiority to an external published baseline is claimed. Optional 5% base-rate benchmark is off.
The official test is a one-time holdout; do not select new hyperparameters, features or thresholds after seeing these results. Any correction causing repeated exposure must be documented.
Repeated-exposure note: none declared

Completion is established only by Cell 15's manifest and full_complete flag.
