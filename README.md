# Zero- and Few-Shot Kanuri Sentiment Classification with Multilingual LLMs

This repository holds the materials for the paper:

> B. M. Saleh, S. Bilgaiyan, and S. Sagnika, "Zero- and Few-Shot Kanuri Sentiment Classification with Multilingual LLMs: Translation Access Outweighs Demonstrations," *under review*, 2026.

We evaluate two instruction-tuned multilingual LLMs, **Qwen3-4B-Instruct-2507** and **Aya-Expanse-8B**, on three-class Kanuri sentiment classification without any parameter updates. Both models see the same frozen 1,795-item KanuriSenti-Extended test set in three input views (Kanuri only, English translation only, and both), under zero-shot and class-balanced six-shot prompting. The six-shot conditions are repeated with three demonstration seeds (42, 123, 456), giving **43,080 validated predictions** in total.

## Main results (macro-F1, seed 42)

| Input view | Shots | Qwen3 4B | Aya Expanse 8B |
|---|---|---|---|
| Kanuri | 0 | 0.2592 | 0.2672 |
| Kanuri | 6 | 0.3617 | 0.3552 |
| English | 0 | 0.7788 | 0.7897 |
| English | 6 | 0.7759 | 0.7764 |
| Bilingual | 0 | 0.6617 | 0.7912 |
| Bilingual | 6 | 0.7726 | 0.7978 |

Across three demonstration seeds, bilingual six-shot macro-F1 averages 0.7640 ± 0.0217 (Qwen3) and 0.7920 ± 0.0144 (Aya). See the paper for paired statistics, supervised baselines and error analysis.

## Repository structure

| Folder | Contents |
|---|---|
| `data/` | Frozen test identifiers, train/development identifiers and dataset checksums |
| `prompts/` | Prompt templates for all six input-view × shot conditions (with placeholders instead of example text) |
| `demonstrations/` | The six training examples selected by each demonstration seed (42, 123, 456) |
| `predictions/` | Raw generations and parsed labels for all 24 model–view–shot–seed conditions |
| `evaluation/` | Inference, parsing, metric and significance-testing scripts |
| `results/` | Metric tables, confusion matrices and paired-test outputs |
| `error_audit/` | IDs, strata, gold labels and model predictions for the 72-item review sample |

Each folder has its own README listing the files it should contain.

## Experimental settings

| Setting | Value |
|---|---|
| Models | `Qwen/Qwen3-4B-Instruct-2507`, `CohereLabs/aya-expanse-8b` |
| Checkpoint revisions | See `data/run_metadata.json` |
| Quantization | 4-bit NF4 |
| Decoding | Greedy (`do_sample=False`), max 16 new tokens |
| Input limit | 3,072 tokens |
| Hardware | 1 × NVIDIA L4 GPU |
| Software | PyTorch 2.14.1+cu130, Transformers 4.57.6 |
| Demonstration seeds | 42 (primary), 123, 456 |
| Bootstrap | 2,000 paired item-level resamples |
| Significance tests | Exact McNemar with Holm correction |

## Reproducing the results

```bash
pip install -r requirements.txt
# 1. Run inference for every condition
python evaluation/<inference_script>.py --model <model_id> --view <kanuri|english|bilingual> --shots <0|6> --seed <42|123|456>
# 2. Parse outputs and compute metrics
python evaluation/<evaluation_script>.py --predictions predictions/
```

Replace the script names above with the actual files in `evaluation/`.

## Data

> **Text release.** This repository currently contains record identifiers, labels, prompts, predictions, scripts and metrics, but **not** the Kanuri text or English translations of KanuriSenti-Extended. The text will be added once the extended corpus is published. All results can be matched to the corpus by record ID.


The source text comes from the KanuriSenti-Extended corpus, which builds on the published KanuriSenti dataset:

> B. M. Saleh, S. Bilgaiyan, and S. Sagnika, "KanuriSenti: A novel dataset for sentiment analysis in the under-resourced Kanuri language," *Data in Brief*, vol. 61, art. 111758, 2025. doi: [10.1016/j.dib.2025.111758](https://doi.org/10.1016/j.dib.2025.111758)

The original KanuriSenti data is available on [Mendeley Data](https://data.mendeley.com/datasets/pcfvh7r6nd/1).

## Citation

If you use these materials, please cite the paper (citation will be updated on publication) and the KanuriSenti dataset above.

## Contact

Bashir Maina Saleh — 2381127@kiit.ac.in
