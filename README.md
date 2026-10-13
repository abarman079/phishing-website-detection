# Lightweight and Interpretable Phishing Website Detection

MSc Computer Science thesis project investigating whether a compact set of URL–HTML registered-domain-consistency features improves phishing website detection beyond conventional URL features under temporal change.

## Research question

> To what extent can a compact and interpretable set of URL–HTML registered-domain-consistency features improve lightweight phishing website detection beyond conventional URL features when evaluated on temporally separated webpages?

## Data and reproducibility

- **Dataset:** [PhreshPhish v1.0.1](https://huggingface.co/datasets/phreshphish/phreshphish); [dataset paper](https://arxiv.org/abs/2507.10854)
- **Pinned dataset revision:** `eabec4b7a66324b79cc8a0ad856d1731dc26fe1a`
- **Official training partition:** 498,255 samples (276,729 benign; 221,526 phishing)
- **Official test partition:** 168,060 samples (91,260 benign; 76,800 phishing)
- **Internal temporal development cutoff:** 2025-07-09 inclusive

| Development partition | Rows | Benign | Phishing | Dates |
|---|---:|---:|---:|---|
| Internal training | 397,480 | 204,388 | 193,092 | 2024-07-02 to 2025-07-09 |
| Internal validation | 100,775 | 72,341 | 28,434 | 2025-07-10 to 2025-09-08 |

The original official train/test partition is preserved. The official train/test boundary has a **same-calendar-day overlap for benign observations** on 2025-09-08; it is not fully strictly later by calendar date. This limitation is reported rather than altering the official split. The internal split is chronological with whole dates kept together.

## Feature design

- **URL-only representation (15):** lexical/structural URL attributes, including length, hostname/path/query properties, punctuation/digit counts, subdomains, query parameters, IP/punycode indicators and entropy.
- **Hybrid representation (25):** the same 15 URL features and 10 retained HTML registered-domain-consistency features. HTML features characterize external links, form actions, scripts, images, iframe/frame resources, linked resources, distinct external domains, and the dominant reference-domain mismatch.
- Stored HTML is parsed statically. No live webpages or linked resources are fetched, and no scripts are executed.
- Public-Suffix-List-aware comparisons use `tldextract==5.3.0` and a fixed offline PSL snapshot.

Notebook 2 retained **H08 overall external-reference ratio**, based on the frozen `internal_train` decision rule: maximum absolute component Spearman correlation **0.67346** (< 0.90) and VIF **5.72318** (<= 10). All 10 HTML candidates were retained.

## Model configurations

| ID | Estimator | Predictors | Selected settings in Notebook 3 |
|---|---|---|---|
| M0 | DummyClassifier (most frequent) | Majority baseline | — |
| M1 | Logistic Regression | 15 URL | `C=10.0` |
| M2 | Logistic Regression | 25 URL+HTML | `C=10.0` |
| M3 | Random Forest | 15 URL | `n_estimators=300`, `max_depth=20` |
| M4 | Random Forest | 25 URL+HTML | `n_estimators=300`, `max_depth=20` |

Hyperparameters were selected by **Average Precision (AP)** on the later internal temporal validation partition only. Logistic Regression uses median imputation and scaling inside a training-fitted pipeline. Random Forest uses median imputation inside its pipeline. Fixed seed: 42. After model selection, all five models were refitted on all 498,255 official training rows and serialized in the saved Kaggle Notebook 3 FULL output.

### Notebook 3 results — internal temporal validation only

| Model | Average Precision | F1 | MCC | ROC-AUC |
|---|---:|---:|---:|---:|
| M0 Dummy | 0.2822 | 0.0000 | 0.0000 | 0.5000 |
| M1 LR URL | 0.7362 | 0.6479 | 0.5025 | 0.7806 |
| M2 LR URL+HTML | 0.8148 | 0.7188 | 0.6081 | 0.8723 |
| M3 RF URL | 0.8230 | 0.7447 | 0.6568 | 0.8690 |
| M4 RF URL+HTML | 0.8949 | 0.8227 | 0.7714 | 0.9292 |

**These are development/validation results, not final official-test results.** The frozen official-test evaluation is deferred to Notebook 4. No inferential significance or final deployment claims should be drawn from these validation values alone.

## Notebook status

| Notebook | Source | Status |
|---|---|---|
| 1. Data audit and URL features | [`notebooks/01_data_audit_and_url_features.ipynb`](notebooks/01_data_audit_and_url_features.ipynb) | FULL complete |
| 2. HTML consistency features | [`notebooks/02_html_domain_consistency_features.ipynb`](notebooks/02-html-domain-consistency-features.ipynb) | FULL complete; `ready_for_nb03=true` |
| 3. Temporal validation and model selection | [`notebooks/03_temporal_validation_and_model_selection.ipynb`](notebooks/03_temporal_validation_and_model_selection.ipynb) | FULL complete; `ready_for_nb04=true` |
| 4. Official-test evaluation and interpretation | [`notebooks/04_final_temporal_evaluation_and_interpretation.ipynb`](notebooks/04_final_temporal_evaluation_and_interpretation.ipynb) | FULL complete; `full_complete=true` |

## Results directories

- [`Results/Notebook 1/`](Results/Notebook%201/) — URL feature audit, figures, descriptive statistics, reproducibility manifests.
- [`Results/Notebook 2/`](Results/Notebook%202/) — static-HTML diagnostics, H08 decision, feature manifest, figures and provenance.
- [`Results/Notebook 3/`](Results/Notebook%203/) — internal temporal validation metrics, candidate/selection results, six figures (F13–F18), model-configuration freeze, checksums, and FULL-run manifest.
- [`environment/`](environment/) — package versions used by each notebook.
- [`Results/Notebook 4/`](Results/Notebook%204/) — final official-test metrics, paired McNemar tests, bootstrap confidence intervals, feature interpretation, efficiency analysis, F19–F30 figures, and the FULL-run manifest.

## Important: large artifacts are stored outside Git

Generated `*.parquet`, `*.joblib`, and ZIP archives are excluded by `.gitignore`. This is intentional. Preserve the complete Kaggle FULL saved versions and private backup copies, particularly:

**Notebook 2, for Notebook 4:**

```text
features/hybrid_features_test.parquet
```

**Notebook 3, for Notebook 4:**

```text
models/M0_dummy_majority.joblib
models/M1_lr_url.joblib
models/M2_lr_hybrid.joblib
models/M3_rf_url.joblib
models/M4_rf_hybrid.joblib
manifests/frozen_model_config.json
manifests/manifest_nb03.json
```

The files under `Results/Notebook 3/manifests/` record expected model checksums, fit scope and exact package versions. **Manifest records alone do not replace the actual fitted `.joblib` model files.** Do not commit the large fitted Random Forest files directly to GitHub; they exceed the normal single-file size limit.

The committed notebook source is designed to start in `RUN_MODE="SMOKE"` for safe reproducibility. The linked FULL results reflect a separately completed Kaggle run. Before rerunning FULL, switch that setting deliberately and use the saved Notebook 2 FULL training inputs. Notebook 3 must not read the official test dataset.

## Evaluation boundary and academic use

## Final official-test results — Notebook 4

Notebook 4 completed the one-time evaluation of all five frozen models on 168,060 official-test observations.

| Model | AP | F1 | MCC |
|---|---:|---:|---:|
| M0 Dummy | 0.4570 | 0.0000 | 0.0000 |
| M1 LR URL | 0.8829 | 0.7939 | 0.6260 |
| M2 LR URL+HTML | 0.9324 | 0.8457 | 0.7195 |
| M3 RF URL | 0.9549 | 0.8601 | 0.7518 |
| M4 RF URL+HTML | 0.9780 | 0.9131 | 0.8510 |

Both hybrid models improved over their corresponding URL-only models. Two predeclared McNemar comparisons supported differences in classification correctness (p < 0.001). Paired bootstrap 95% confidence intervals for AP, F1 and MCC improvements excluded zero.

The official test has a documented same-calendar-day boundary limitation. No model refitting or hyperparameter tuning was performed after test exposure.

See `Results/Notebook 4/reports/final_result_values_for_thesis.md` for detailed results and limitations.
