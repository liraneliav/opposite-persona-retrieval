# Efficient Retrieval of Opposite Personas from Large-Scale Datasets on Controversial Topics

This repository contains the anonymous implementation and experimental artifacts for the short paper **"Efficient Retrieval of Opposite Personas from Large-Scale Datasets on Controversial Topics"**.

The project builds contradiction-aware persona repositories from a large Reddit ChangeMyView corpus and compares the proposed framework against several baseline persona-retrieval methods.

## Repository contents

```text
.
├── data/final_repositories/        # Final 300-per-topic persona repositories
├── results/                        # Final result tables and statistical tests
├── dataset_construction/           # Notebooks for building the proposed repositories
├── evaluation/                     # Retrieval-baseline comparison and significance-test notebooks
├── docs/                           # Data and reproducibility documentation
├── requirements.txt
└── .gitignore
```

## Raw dataset

The raw Reddit dataset is **not included** in this repository due to size. It is publicly available from Zenodo:

- Webis ChangeMyView Corpus 2020: https://zenodo.org/records/3778298

After downloading, place the raw comments file at:

```text
data/raw/comments_cmv.csv
```

The expected raw columns are:

```text
comment_id, author, comment_text, topic_title, thread_id, delta
```

## Installation

Create a fresh environment and install dependencies:

```bash
python -m venv .venv
source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

For notebooks:

```bash
python -m ipykernel install --user --name opposite-persona-retrieval
jupyter notebook
```

## API key

Several steps use an LLM API for stance labeling, persona refinement, and response generation.

## Reproducing the dataset construction

Run the notebooks in order:

```text
dataset_construction/step1_separate_topics_in_dataset.ipynb
dataset_construction/step2_gpt_top8000_and_stance_labeling.ipynb
dataset_construction/step3_diversity_selection.ipynb
dataset_construction/step4_gpt_classification.ipynb
```

The final outputs are the three balanced repositories in `data/final_repositories/`:

```text
BLM_final_300_150pro_150anti_gpt.csv
guns_final_300_150pro_150anti_gpt.csv
same_sex_marriage_final_300_150pro_150anti_gpt.csv
```

Each contains 150 pro and 150 anti persona records.

## Reproducing the evaluation

The included evaluation focuses on retrieval-based baselines reported in the paper: Semantic Persona Retrieval (SPR), Partner Matters, COO, Persona-DB, and the proposed framework.


To rerun the retrieval-baseline comparison, use:

```text
evaluation/compare_methods_baseline_final.ipynb
```

To rerun the statistical significance tests, use:

```text
evaluation/baselines_statistical_test_final.ipynb
```

The final reported results are included in `results/` so the paper tables can be inspected without rerunning API-dependent experiments.
The reported NLI contradiction scores in `results/full_results.csv` were computed with `MoritzLaurer/mDeBERTa-v3-base-mnli-xnli`, which is separate from the internal method NLI reranker (`MoritzLaurer/multilingual-MiniLMv2-L6-mnli-xnli`).
The included result tables retain only the metrics reported in the paper: NLI contradiction, opposite-stance accuracy, and runtime.


## Final result files

```text
results/full_results.csv
results/summary_results.csv
results/by_topic_results.csv
results/nli_significance_table.csv
results/stance_significance_table.csv
```

## Citation

If citing the dataset, use:

```bibtex
@dataset{webis_cmv_2020,
  author = {Kolyada, Nikolay and Al-Khatib, Khalid and Völske, Michael and Syed, Shahbaz and Stein, Benno},
  title = {Webis ChangeMyView Corpus 2020 (Webis-CMV-20)},
  year = {2020},
  doi = {10.5281/zenodo.3778298},
  url = {https://zenodo.org/records/3778298}
}
```
