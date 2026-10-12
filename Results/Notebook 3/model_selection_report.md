# Notebook 3 model-selection report

Mode: FULL

Internal temporal validation only; these are not final official-test results.

AP is sklearn average_precision_score, not trapezoidal precision-recall area.

Maximum internal-validation average_precision_score; exact ties retain first candidate in declared order

No threshold optimization; predict() uses a nominal 0.5 boundary, ties to class 0.

Refit scope: all official train; rows: 498,255.

M0 validation fit used internal_train; saved M0 uses the final refit scope.

No test dataset/schema loaded or evaluated; official boundary caveat is retained from the handoff.

All RF fits kept 300 trees; model objects were released between fits.

RSS before/after are snapshots, not measured peak memory. Times are real wall-clock fit times.

Small SMOKE RF scores are provisional code checks, not thesis findings.

Selected settings:

- M0: {}; validation AP=0.282153

- M1: {"C": 10.0}; validation AP=0.736194

- M2: {"C": 10.0}; validation AP=0.814833

- M3: {"max_depth": 20}; validation AP=0.822998

- M4: {"max_depth": 20}; validation AP=0.894859

Blockers: []

NB4 requires FULL models, frozen_model_config.json and manifest_nb03.json.

Preserve private Kaggle outputs; do not commit large joblib models to the public repository.
