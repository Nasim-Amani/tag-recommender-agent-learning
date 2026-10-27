# 🏷️ Tag Recommender Agent: Learning

A step-by-step learning repo: from NLP basics to an **LLM-based agent that recommends product tags** from vendor text.
Built alongside my research proposal on *autonomous tag recommendation using syntactic patterns and neighbouring tag attributes*.

**Background:** time-series forecasting (LSTM, Transformer) → learning NLP, recommender systems and AI agents.
**Data:** [Amazon Reviews 2023](https://amazon-reviews-2023.github.io/) item metadata, category *All_Beauty* (vendor text → tags from the `details` attributes).

## 🗺️ Roadmap
| Phase | Weeks | Topic | What I learn / build | Notebook | Status |
|---|---|---|---|---|---|
| 1 | 1–3 | **NLP foundations** | Text cleaning, tokenization, TF-IDF, embeddings, spaCy POS & dependency parsing, syntactic patterns | [Phase1_NLP_Foundations](Phase1_NLP_Foundations.ipynb) | ✅ Done |
| 2 | 3–5 | **Recommenders & multi-label** | Multi-label classification, leak-free vendor split, metrics (P@k, F1, MAP), baselines: popularity, TF-IDF + LR, DistilBERT, error analysis | [Phase2_Baselines](Phase2_Baselines.ipynb) | ✅ Done |
| 3 | 5–6 | **Embeddings & retrieval** | MiniLM + FAISS (dense) and TF-IDF (sparse) retrieval, kNN tag propagation, hybrid (LR + kNN), leakage check, rare & implicit tags, explainability | [Phase3_Retrieval](Phase3_Retrieval.ipynb) | ✅ Done |
| 4 | 6–8 | **Tag graphs** | Co-occurrence, PMI / NPMI / P(b\|a), networkx graph & communities, graph re-ranking, conflict filter, ablation, node2vec-style tag embeddings | [Phase4_Tag_Graphs](Phase4_Tag_Graphs.ipynb) | ✅ Done |
| 5 | 8–10 | **LLMs** | Qwen2.5-1.5B: zero/few-shot prompting, JSON output, RAG, abstention, LoRA fine-tuning | [Phase5_LLMs](Phase5_LLMs.ipynb) | 🟡 In progress |
| 6 | 10–12 | **Agent** | Tool calling, ReAct loop, verifier, memory → end-to-end tag agent | — | ⬜ |
| 7 | 12–16 | **Experiments & paper** | Ablations, cross-domain tests (AE-110K / MAVE), error analysis, cost/latency, write-up | — | ⬜ |

## 🏁 Results so far (test set, vendor split)

| Model | micro-F1 | macro-F1 | P@3 | MAP | Cost |
|---|---|---|---|---|---|
| Popularity (no text) | 0.152 | 0.007 | 0.134 | 0.198 | — |
| TF-IDF + Logistic Regression | 0.579 | 0.306 | 0.463 | 0.683 | ~50 s, CPU |
| DistilBERT (fine-tuned) | **0.589** | **0.318** | 0.463 | 0.686 | ~14 min, GPU |
| kNN (MiniLM + FAISS) | 0.526 | 0.266 | 0.418 | 0.613 | ~1 min, GPU |
| kNN (TF-IDF) | 0.541 | 0.269 | 0.430 | 0.634 | ~1 min, CPU |
| Hybrid (LR + kNN, α = 0.9) | 0.580 | 0.304 | **0.465** | **0.687** | — |
| + tag-graph re-ranking | = no change (best β = 0) | | | | |
| LLM variants | *Phase 5 (500-product sample)* | | | | |

**A ceiling at P@3 ≈ 0.46:** a deeper text model, similar products and tag relations all come from the **same vendor data** and mostly repeat what TF-IDF + LR already knows.

## 🔑 Key findings so far
**Data (Phase 1)**
- **82%** of products have only a short title (median 22 words).
- Only **~37%** of tag values are written explicitly in the text; grammar patterns alone recover **~10%** → motivates a **hybrid** of patterns, retrieval and LLM reasoning.
- Tags follow a **long tail** → report **macro-F1** as well as micro-F1.

**Baselines (Phase 2)**
- After cleaning: **19,408 products, 214 descriptive tags, 2.26 tags/product**; split **by vendor**.
- Reading the text helps: models are **~3× better** than predicting popular tags.
- **DistilBERT ≈ TF-IDF + LR** at ~20× the cost → the bottleneck is the **information in short titles**.
- **36%** of "wrong" predictions have their value written in the text → vendor tags are **incomplete**.
- Error types: label noise, implicit claims, word confusion, **negation** ("No Clip"), cleaning leftovers.

**Retrieval (Phase 3)**
- **kNN alone < LR** (P@3 0.42–0.43 vs 0.46); **words beat meaning** for finding neighbours (TF-IDF kNN > MiniLM kNN).
- **Hybrid ≈ LR** (+0.002 P@3): neighbours found by text mostly repeat the text.
- Helps only on **rare tags** (< 50 examples, 40% of tags): F1 +11%; **no gain on implicit tags**.
- Random split → **27.8%** same-vendor nearest neighbours; inflates macro-F1 by ~10% → vendor split needed.
- kNN gives **evidence** for each tag; low neighbour similarity signals when to **abstain**.

**Tag graph (Phase 4)**
- From tag co-occurrence alone, the graph found **6 product families** (makeup, skin care, nails, hair extensions, lashes & accessories, hair care): **211 tags, 1,038 edges**.
- Clear product logic: `human hair` ⇒ some `Hair Type` (straight 43% · wavy 34% · curly 19%).
- **Re-ranking does not help** (best β = 0; boosting hurts rare tags): the graph says **which attribute**, not **which value**, and LR already learned the co-occurrences.
- Useful for the agent as **explanation**, **completeness** and **conflict** checks; tag embeddings find alternatives (e.g. installation types).

## 📁 Structure
```
├── README.md
├── Phase1_NLP_Foundations.ipynb
├── Phase2_Baselines.ipynb
├── Phase3_Retrieval.ipynb
├── Phase4_Tag_Graphs.ipynb
├── Phase5_LLMs.ipynb
└── ...                       # one notebook per phase
```
*Data and model files are not included (size + dataset license); notebooks download the data and save outputs to Google Drive.*
