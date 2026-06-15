# Critical Evaluation of "A Subset Feature Elimination Mechanism for Intrusion Detection System"

Final project for **Data Science in Cyber** (Dr. Uri Itai) — Adam Karain (ID 318407889), June 2026.

## Project Description

This project critically evaluates a published article on feature selection for network intrusion detection and reproduces its experiments on the NSL-KDD benchmark.

The article proposes selecting 11–15 of NSL-KDD's features with a univariate ANOVA F-test filter followed by Recursive Feature Elimination (RFE) around a decision tree, applied separately to each attack class (DoS, Probe, R2L, U2R) as a binary problem, and reports ≈99.7–99.9% accuracy, precision and recall plus an order-of-magnitude training speed-up.

We rebuild the pipeline faithfully (ANOVA filter **and** RFE, the article's own per-class feature counts, leakage-free `scikit-learn` pipelines) and score it three ways. The article's numbers reproduce almost exactly under cross-validation — and, importantly, the article **does** use the official test set: its Methodology states the test data was used for prediction, and its confusion-matrix totals equal the `KDDTest+` class sizes. The flaw is not an untouched test set but the **evaluation protocol**: the test set is cross-validated (pooled) rather than held out, so the novel attack types `KDDTest+` was built to isolate leak into the model's own training folds. We show this directly — cross-validating `KDDTest+` itself reproduces the article's R2L figure (97.7% vs. a claimed 97.4%) — while being careful not to attribute that exact procedure to the authors, who released no code. Under the benchmark's **intended held-out protocol**, the same faithful pipeline collapses (R2L recall **11.1%**, 95% CI ≈ 10–12%, against a claimed 97.4%). The training speed-up is confirmed; the in-distribution performance reproduces but does not generalize.

## Evaluated Source

* **Article:** H. Nkiama, S. Z. Mohd Said, M. Saidu, "A Subset Feature Elimination Mechanism for Intrusion Detection System," *IJACSA*, vol. 7, no. 4, 2016 — [PDF](https://thesai.org/Downloads/Volume7No4/Paper_19-A_Subset_Feature_Elimination_Mechanism_for_Intrusion_Detection_System.pdf). The original authors released **no code**.
* **Third-party reference implementation (not the authors'):** [CynthiaKoopman/Network-Intrusion-Detection](https://github.com/CynthiaKoopman/Network-Intrusion-Detection) — an independent reproduction, used only as corroborating evidence for how the article's numbers can arise.

## Dataset Source

**NSL-KDD** (Tavallaee et al., 2009): 125,973 training records (`KDDTrain+`) and 22,544 test records (`KDDTest+`), 41 features plus an attack-type label and a difficulty score. `KDDTest+` deliberately contains 17 attack types absent from training (29.2% of its attack records).

* Official mirror used: [defcom17/NSL_KDD](https://github.com/defcom17/NSL_KDD)
* The three files required by the notebook (`KDDTrain+.txt`, `KDDTest+.txt`, `Field Names.csv`) are included in `data/` so the notebook runs with no downloads.

## Repository Contents

| File | Description |
|---|---|
| `NSL_KDD_critical_evaluation.ipynb` | The full analysis: data loading, EDA, feature engineering, faithful reproduction of the article's pipeline, model training, error analysis, conclusions (executed, with outputs) |
| `report.pdf` | The project report (summary, critical evaluation, feature-engineering analysis, reproducibility analysis, experimental results, conclusions, executive summary, summing up) |
| `data/` | NSL-KDD files used by the notebook |
| `requirements.txt` | Pinned Python dependencies |

## Execution Instructions

Requires Python 3.10+.

```bash
git clone <this-repository-url>
cd <this-repository>
pip install -r requirements.txt
jupyter notebook NSL_KDD_critical_evaluation.ipynb
```

Run all cells top to bottom ("Run All"). Full execution takes about two minutes on a laptop; all random seeds are fixed, so every number and figure reproduces exactly.

## Key Results

Per-class reproduction of the article's pipeline (ANOVA + RFE + decision tree, the article's own feature counts), scored three ways:

| Class | Article (Table IV): acc / prec / rec (%) | Ours — CV on train (%) | Ours — CV on test (%) | Ours — held-out KDDTest+: acc / recall (95% CI) |
|---|---|---|---|---|
| DoS | 99.90 / 99.69 / 99.79 | 99.6 | 99.6 | 88.1 / 73.7 (72.7–74.6) |
| Probe | 99.80 / 99.37 / 99.37 | 99.4 | 99.2 | 88.4 / 55.2 (53.2–57.1) |
| R2L | 99.88 / 97.40 / 97.41 | 99.8 | **97.7** | 79.6 / **11.1** (9.9–12.3) |
| U2R | 99.95 / 99.70 / 99.69 | 99.9 | 99.7 | 99.5 / **34.3** (22.4–44.8) |

Cross-validation on the **test set** reproduces the article (R2L 97.7% ≈ the claimed 97.4%); held out as intended, recall collapses. The U2R interval is wide because `KDDTest+` has only 67 U2R records — uncertainty that the headline "99.5% accuracy" hides.
