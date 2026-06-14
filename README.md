# Critical Evaluation of "A Subset Feature Elimination Mechanism for Intrusion Detection System"

Final project for **Data Science in Cyber** (Dr. Uri Itai) — Adam Karain (ID 318407889), June 2026.

## Project Description

This project critically evaluates a published article on feature selection for network intrusion detection and reproduces its experiments on the NSL-KDD benchmark.

The article proposes selecting 11–15 of NSL-KDD's features with a univariate ANOVA filter followed by Recursive Feature Elimination (RFE) around a decision tree, applied separately per attack class (DoS, Probe, R2L, U2R), and reports ≈99.7–99.9% accuracy, precision and recall.

We reproduce the pipeline end-to-end and evaluate it twice: under the article's own protocol (cross-validation within the training distribution) and on the official `KDDTest+` split, in which 29.2% of attack records belong to attack types absent from training. The article's numbers reproduce almost exactly under its own protocol (U2R: 99.95%, to the second decimal) — and collapse on the official test set (R2L recall: 9.6% versus a claimed 97.4%). The project documents why: the published evaluation retrains models inside the distribution they are scored on, structurally hiding the benchmark's novel attacks. The training speed-up claim is confirmed; the detection-performance claims are rejected.

## Evaluated Source

* **Article:** H. Nkiama, S. Z. Mohd Said, M. Saidu, "A Subset Feature Elimination Mechanism for Intrusion Detection System," *IJACSA*, vol. 7, no. 4, 2016 — [PDF](https://thesai.org/Downloads/Volume7No4/Paper_19-A_Subset_Feature_Elimination_Mechanism_for_Intrusion_Detection_System.pdf)
* **Original GitHub repository (reference implementation):** [CynthiaKoopman/Network-Intrusion-Detection](https://github.com/CynthiaKoopman/Network-Intrusion-Detection)

## Dataset Source

**NSL-KDD** (Tavallaee et al., 2009): 125,973 training records (`KDDTrain+`) and 22,544 test records (`KDDTest+`), 41 features plus an attack-type label and a difficulty score.

* Official mirror used: [defcom17/NSL_KDD](https://github.com/defcom17/NSL_KDD)
* The three files required by the notebook (`KDDTrain+.txt`, `KDDTest+.txt`, `Field Names.csv`) are included in `data/` so the notebook runs with no downloads.

## Repository Contents

| File | Description |
|---|---|
| `NSL_KDD_critical_evaluation.ipynb` | The full analysis: data loading, EDA, feature engineering, model training, reproduction of the article's protocol, error analysis, conclusions (executed, with outputs) |
| `report.pdf` | The project report (summary, critical evaluation, feature-engineering analysis, reproducibility analysis, experimental results, conclusions, executive summary) |
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

| Class | Article: accuracy / precision / recall (%) | Reproduced CV accuracy (%) | Official KDDTest+: accuracy / recall (%) |
|---|---|---|---|
| DoS | 99.90 / 99.69 / 99.79 | 99.97 | 93.7 / 86.7 |
| Probe | 99.80 / 99.37 / 99.37 | 99.88 | 91.1 / 67.7 |
| R2L | 99.88 / 97.40 / 97.41 | 99.92 | 79.3 / **9.6** |
| U2R | 99.95 / 99.70 / 99.69 | 99.95 | 99.5 / **34.3** |
