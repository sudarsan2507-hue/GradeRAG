# GradeRAG

**GradeRAG** is a retrieval-augmented generation (RAG) pipeline that learns from its own
mistakes. Instead of treating each query independently, GradeRAG maintains a growing
**Mistake Knowledge Graph (MKG)**: every time the system answers incorrectly, the failure
is categorised (e.g. *retrieval failure*, *reasoning failure*) and stored. On later queries,
semantically similar past failures are retrieved and used to steer generation away from the
same error.

> **Status:** the paper describing the method is under review. This repository currently
> publishes only the **experimental results and analysis**. The full implementation
> (notebooks for the MKG, the GradeRAG loop, and the evaluation harness) will be added here
> once the paper is published.

## Experimental setup

- **Datasets:** HotpotQA (distractor) and TriviaQA (rc), validation split.
- **Retriever:** `all-MiniLM-L6-v2` sentence embeddings over a FAISS index.
- **Generators evaluated:** GPT-4o-Mini, Nemotron-Mini, Qwen2.5-VL.
- **Conditions:** `baseline` = standard RAG · `graderag` = RAG + Mistake Knowledge Graph.
- **Metrics:** exact-match accuracy, token-level F1, faithfulness, and MAR (mean answer
  reliability) as a function of MKG size.

## Headline results

Exact-match accuracy, Standard RAG → GradeRAG:

| Model | Dataset | Baseline | GradeRAG | Δ |
|---|---|---:|---:|---:|
| GPT-4o-Mini | HotpotQA | 0.58 | **0.88** | +0.30 |
| GPT-4o-Mini | TriviaQA | 0.73 | **0.87** | +0.14 |
| Nemotron-Mini | HotpotQA | 0.60 | **0.80** | +0.20 |
| Nemotron-Mini | TriviaQA | 0.73 | **0.87** | +0.13 |
| Qwen2.5-VL | HotpotQA | 0.60 | **0.73** | +0.13 |
| Qwen2.5-VL | TriviaQA | 0.80 | 0.67 | −0.13 |

GPT-4o-Mini runs use 200 questions per dataset; the open models use a smaller pilot set
(~15 questions) and should be read as directional. The MKG growth curves in
[`Raw/`](Raw/) show reliability rising as more failure patterns are accumulated.

## Repository layout

| Path | Contents |
|---|---|
| [`Raw/`](Raw/) | Per-question result CSVs (`results_<model>_<dataset>_<condition>.csv`), qualitative answer comparisons (`examples_*.json`), and generated figures (accuracy / F1 / MAR bars, MKG growth curves). |
| [`Resprentation/`](Resprentation/) | Aggregated spreadsheet and summary charts. |
| `Code/` | *Not published yet — added on paper publication.* |

### CSV columns

`id, question, ground_truth, retrieved_docs, predicted, correct, f1_score, faithfulness`
— GradeRAG files add `mkg_matched` (whether a stored failure pattern fired for that query).

## Citation

Citation details will be added when the paper is published.
