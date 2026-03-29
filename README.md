# COMP5318 Assignment 1 – Rice Classification

This repository implements the required COMP5318 Assignment 1 classification pipeline on the Rice dataset. The work follows the official assignment structure and focuses on correctness, reproducibility, and concise reporting.

The **main submission artefact** is the notebook:
`comp5318-a1-2026-template.ipynb`.

## Repository Structure

- `comp5318-a1-2026-template.ipynb`: main assignment notebook (preprocessing, Part 1, Part 2, Part 3).
- `rice-final2.csv`: primary dataset used for reported assignment results.
- `test-before.csv`: local runnability check dataset only (not part of final reported results).
- `requirement.txt`: Python package dependencies.
- `Description.pdf`: assignment brief.

## Installation

Create/activate a Python environment, then install dependencies:

```bash
pip install -r requirement.txt
```

## Run the Notebook

1. Start Jupyter in the repository root.
2. Open `comp5318-a1-2026-template.ipynb`.
3. Run all cells from top to bottom.

The final polished notebook should report results for `rice-final2.csv`. Any `test-before.csv` checks are for local validation only.

## Methods Summary

- **Preprocessing**: mean imputation (`SimpleImputer`), min-max scaling to `[0,1]` (`MinMaxScaler`), and label conversion (`class1 -> 0`, `class2 -> 1`).
- **Part 1 (baseline)**: Logistic Regression and Gaussian Naive Bayes with 10-fold stratified cross-validation.
- **Part 2 (tuned)**: KNN, Decision Tree, AdaBoost, Gradient Boosting, Random Forest, and SVM tuned using `GridSearchCV` with stratified train/test split.
- **Part 3**: short reflection and discussion comparing model behavior and evaluation choices.

