# Lightweight and Interpretable Phishing Website Detection

MSc Computer Science thesis project investigating whether **selected URL–HTML registered-domain-consistency features improve phishing website detection over conventional URL features under temporal change**.

## Research question

> To what extent can a compact and interpretable set of URL–HTML registered-domain-consistency features improve lightweight phishing website detection beyond conventional URL features when evaluated on temporally separated webpages?

## Dataset

- **Source:** [PhreshPhish v1.0.1](https://huggingface.co/datasets/phreshphish/phreshphish)
- **Dataset paper:** [PhreshPhish (arXiv:2507.10854)](https://arxiv.org/abs/2507.10854)
- **Pinned dataset revision:** `eabec4b7a66324b79cc8a0ad856d1731dc26fe1a`
- **Official train:** 498,255 samples (276,729 benign; 221,526 phishing)
- **Official test:** 168,060 samples (91,260 benign; 76,800 phishing)

The original official train/test split remains unchanged. A chronological development split is constructed **within official train**, with an inclusive cutoff of `2025-07-09`:

| Development partition | Rows | Dates |
|---|---:|---|
| Internal training | 397,480 | 2024-07-02 through 2025-07-09 |
| Internal validation | 100,775 | 2025-07-10 through 2025-09-08 |

The official train/test boundary is **not strictly separated by calendar day for benign samples** (both sides include `2025-09-08`). This limitation is documented, rather than changing the prescribed dataset split.

## Feature sets

- **URL-only:** 15 lexical/structural URL features.
- **URL + HTML:** the same 15 URL features plus 10 retained, interpretable URL–HTML registered-domain-consistency features.

The HTML group includes external-reference ratios for anchors, forms, scripts, images, iframes and link resources; invalid form-action ratio; overall external-reference ratio; number of unique external registered domains; and dominant resource-domain mismatch.

HTML is parsed **statically from the dataset**. The notebooks do not visit the listed websites, fetch linked resources or execute scripts. Public-Suffix-List-aware registered-domain comparisons use `tldextract==5.3.0` with a pinned offline snapshot.

Notebook 2 retained the overall external-reference feature (H08): maximum absolute Spearman correlation with component ratios = **0.67346**; VIF = **5.72318**, below the predeclared thresholds of `0.90` and `10`, respectively. The decision used **internal training only**.

## Models and planned comparison

The model-comparison stage will evaluate:

| ID | Model | Predictor set |
|---|---|---|
| M0 | DummyClassifier (most frequent) | Baseline reference |
| M1 | Logistic Regression | URL-only |
| M2 | Logistic Regression | URL + retained HTML |
| M3 | Random Forest | URL-only |
| M4 | Random Forest | URL + retained HTML |

**As of Notebook 2, no classification-model results have been reported.** Notebook 3 will perform temporal validation and model selection. Notebook 4 will conduct the final official-test evaluation, interpretation and statistical comparison.

## Notebooks and status

| Notebook | Purpose | Status |
|---|---|---|
| [`notebooks/01_data_audit_and_url_features.ipynb`](notebooks/01_data_audit_and_url_features.ipynb) | Audit and extract 15 URL features | Full run completed |
| [`notebooks/02_html_domain_consistency_features.ipynb`](notebooks/02_html_domain_consistency_features.ipynb) | Extract candidate HTML features, quality checks and training-only H08 decision | Full run completed; `ready_for_nb03=true` |
| `03_temporal_validation_and_model_selection.ipynb` | Internal temporal training/validation of M0–M4 | Planned |
| `04_final_temporal_evaluation_and_interpretation.ipynb` | Frozen official-test evaluation and interpretation | Planned |

## Results and reproducibility

- [`Results/Notebook 1/`](Results/Notebook%201/) — URL-feature audit, figures, tables and manifest.
- [`Results/Notebook 2/`](Results/Notebook%202/) — HTML diagnostics, figures, feature-selection JSON, package provenance and FULL-run manifest.
- [`environment/`](environment/) — recorded notebook dependencies.

Large generated Parquet files are **excluded from Git tracking** through `.gitignore`. The recorded FULL-run manifests contain their SHA256 checksums and row counts. Preserve the original Kaggle saved outputs externally and attach the FULL outputs as Kaggle inputs for subsequent notebooks.

### Notebook 3 required input artifacts

From the saved **Notebook 2 FULL** Kaggle output:

```text
features/hybrid_features_train.parquet
manifests/final_feature_set.json
manifests/manifest_nb02.json
```

Also preserve `features/hybrid_features_test.parquet` for Notebook 4. **Do not use the official test partition to select features, train models or tune hyperparameters.**

## Reproducibility and responsible use

The dataset files are loaded from the pinned PhreshPhish revision. Analyses record software versions and artifact hashes. This is an offline academic research pipeline, not a tool for interacting with live phishing websites.

