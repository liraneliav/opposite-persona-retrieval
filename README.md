# Retrieving Opposing Personas from Controversial Online Discussions

**Anonymous research code and experimental artifacts** for *Retrieving Opposing Personas from Controversial Online Discussions* (under anonymous review).

This repository implements a contradiction-aware framework for constructing compact, balanced persona repositories from online discussions and retrieving personas that oppose a user's stated position. The study covers three Reddit discussion topics: Black Lives Matter (BLM), gun control, and same-sex marriage. And evaluates retrieval quality on 600 opinion test cases, contradiction ranking on SNLI, and a pilot user study.

## Method overview

1. **Offline construction:** identify topic-relevant Reddit comments with lexical and embedding-based retrieval; label stance; select representative, diverse pro/anti comments; and refine the final repositories using an LLM. Each final topic repository contains **150 pro and 150 anti** records.
2. **Online retrieval:** select the opposite-stance repository, retrieve query-relevant candidates, use NLI contradiction scoring to shortlist candidates, and use an LLM to choose an opposing persona.
3. **Evaluation:** compare with prompting-only and retrieval-based baselines; separately assess ground-truth contradiction ranking with SNLI; conduct offline ablation and weight-sensitivity analyses.

## Repository structure

```text
.
├── dataset_construction/
│   ├── step1_separate_topics_in_dataset.ipynb
│   ├── step2_gpt_top8000_and_stance_labeling.ipynb
│   ├── step3_diversity_selection.ipynb
│   └── step4_gpt_classification.ipynb
├── evaluation/
│   └── baseline_comparison_600_testcases.ipynb
├── SNLI_ground_truth/
│   └── compare_methods_ground_truth.ipynb
├── ablation/
│   ├── A1_random_top8000_only_resumable.ipynb
│   ├── A2_no_LLM_stance_filtering.ipynb
│   ├── A3_no_diversity.ipynb
│   ├── A4_no_LLM_persona_refinement.ipynb
│   └── online_ablation_A1_A2_A3_A4.ipynb
├── sensitivity/
│   └── sensitivity_600.ipynb
├── data/final_repositories/
│   ├── BLM_final_300_150pro_150anti_gpt.csv
│   ├── guns_final_300_150pro_150anti_gpt.csv
│   └── same_sex_marriage_final_300_150pro_150anti_gpt.csv
├── results/
│   ├── persona_comparison_full_results_600cases.csv
│   ├── persona_comparison_full_summary_600cases.csv
│   ├── persona_comparison_by_topic_600cases.csv
│   ├── baseline_nli_statistical_tests_600cases.csv
│   └── baseline_stance_statistical_tests_600cases.csv
├── docs/
│   ├── DATA.md
│   └── REPRODUCIBILITY.md
├── requirements.txt
└── LICENSE
```

## Data

The source corpus is the [Webis ChangeMyView Corpus 2020](https://zenodo.org/records/3778298), which is **not bundled** with this repository. The notebooks use derived datasets and indices, so downloading the corpus alone does **not** yet provide a fully automated end-to-end reproduction. See [`docs/DATA.md`](docs/DATA.md) for the documented input format and the published final persona repositories. The repository includes the three final 300-record persona CSVs; their original Reddit text and author fields are public-source data and are not anonymized participant data.

For the controlled benchmark, use [Stanford Natural Language Inference (SNLI)](https://huggingface.co/datasets/stanfordnlp/snli). The paper evaluates 2,561 premises with one entailment, neutral, and contradiction candidate per premise; this tests the contradiction-ranking component, not the full offline construction pipeline.

## Installation

Python 3.11 is a suggested environment.

```bash
python -m venv .venv
# macOS/Linux: source .venv/bin/activate
# Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook
```

Some notebooks require API access for LLM inference and local access to intermediate data or model indices. **Do not commit API keys, service credentials, personal endpoints, or raw participant records.** Check each notebook's configuration cells and the provider-specific environment variables before running. Different API deployments or model versions may change outputs and runtime.

## Experiments

| Paper experiment | Notebook(s) / artifacts | Scope |
| --- | --- | --- |
| Offline persona construction (Sec. 3.1–3.3) | `dataset_construction/step1_...` through `step4_...` | Three Reddit topics; 150 pro + 150 anti per topic |
| Reddit baseline comparison (Sec. 4.3, Table 1) | `evaluation/baseline_comparison_600_testcases.ipynb`; `results/persona_comparison_*` | 600 test opinions, 200 per topic |
| SNLI ground-truth benchmark (Sec. 4.1.2, Table 2) | `SNLI_ground_truth/compare_methods_ground_truth.ipynb` | 2,561 premises; three candidate relations |
| Offline ablation (Sec. 5.2, Fig. 3) | `ablation/A1_...` to `A4_...`, `online_ablation_A1_A2_A3_A4.ipynb` | Individual pipeline-component variants; see note below |
| Weight sensitivity (Sec. 5.3, Fig. 4) | `sensitivity/sensitivity_600.ipynb` | Vary persona-quality weights across 600 cases |
| Pilot user study (Sec. 4.1.3, Fig. 2) | Analysis scripts/data **not included** | BLM and gun control; aggregate results described in paper |

**Ablation scope:** The paper's Figure 3 discusses A1–A3; the repository also contains an A4 refinement-ablation notebook. A4 should not be presented as a reported figure result unless separately documented.

### Baselines and evaluation measures

The Reddit comparison includes **GPT Zero-Shot**, **GPT Few-Shot**, **Semantic Persona Retrieval (SPR)**, **Partner Matters**, **Chain-of-Opinion (COO)**, **Persona-DB**, and **our framework**. It reports mean NLI contradiction score, opposite-stance accuracy, and runtime. Statistical comparisons use paired Wilcoxon tests for NLI and McNemar tests for stance accuracy, with Holm correction.

The **evaluation NLI model** is `MoritzLaurer/mDeBERTa-v3-base-mnli-xnli`, distinct from the **internal retrieval reranker** `MoritzLaurer/multilingual-MiniLMv2-L6-mnli-xnli`.

SNLI results are evaluated with Accuracy, Recall@2, Mean Reciprocal Rank (MRR), and runtime.

## Reproducibility and current limitations

- The CSVs under `results/` support inspection of the published Reddit comparison without repeating expensive model calls.
- Intermediate Reddit data, embedding arrays, and HNSW indices are not included. Exact end-to-end regeneration requires their preprocessing code and input/output mapping.

See [`docs/REPRODUCIBILITY.md`](docs/REPRODUCIBILITY.md) for further notes. Results may vary with hardware, stochastic sampling, model revisions, and API configuration.

## Data ethics and citation

The persona repositories contain text originating from public online discussions. Respect the source corpus terms and avoid linking released data to participants in the pilot study. The participant-level user-study data are not included.

For the source corpus, cite the [Webis ChangeMyView Corpus 2020](https://zenodo.org/records/3778298). Citation details for the paper itself will be provided after the anonymous review process.

## License

See [`LICENSE`](LICENSE) for the repository's code license. Third-party datasets, model weights, and APIs remain subject to their own terms.
