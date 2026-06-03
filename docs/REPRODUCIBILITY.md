# Reproducibility Guide

## Minimal reproduction

To inspect the reported results without rerunning costly LLM calls, use the CSV files in `results/`.

## Full reproduction

1. Download the raw Reddit corpus from Zenodo.
2. Place it at `data/raw/comments_cmv.csv`.
3. Install dependencies with `pip install -r requirements.txt`.
4. Run dataset construction notebooks in order.
5. Run `evaluation/compare_methods_baseline_final.ipynb` to reproduce the retrieval-baseline comparison used in the paper.
6. Run `evaluation/baselines_statistical_test_final.ipynb`.

## Important computational notes

The full pipeline performs LLM calls for stance labeling, persona refinement, retrieval-baseline generation, and evaluation. Runtime and cost depend on the selected model and API provider. The final results used in the paper are included to support transparent inspection without requiring API calls. The public result CSVs retain only the metrics reported in the article: NLI contradiction, opposite-stance accuracy, and runtime.
