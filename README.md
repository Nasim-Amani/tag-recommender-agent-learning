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
| 3 | 5–6 | **Embeddings & retrieval** | sentence-transformers + FAISS, kNN tag propagation, hybrid (LR + kNN), leakage check, rare & implicit tags, explainability | [Phase3_Retrieval](Phase3_Retrieval.ipynb) | 🟡 In progress |
| 4 | 6–8 | **Tag graphs** | Tag co-occurrence graph (PMI), graph re-ranking, optional GCN | — | ⬜ |
| 5 | 8–10 | **LLMs** | Prompting, structured JSON output, RAG, LoRA fine-tuning, abstention | — | ⬜ |
| 6 | 10–12 | **Agent** | Tool calling, ReAct loop, verifier, memory → end-to-end tag agent | — | ⬜ |
| 7 | 12–16 | **Experiments & paper** | Ablations, cross-domain tests, error analysis, cost/latency, write-up | — | ⬜ |

## 🏁 Results so far (test set, vendor split)

| Model | micro-F1 | macro-F1 | P@3 | MAP | Cost |
|---|---|---|---|---|---|
| Popularity (no text) | 0.156 | 0.007 | 0.149 | 0.199 | — |
| TF-IDF + Logistic Regression | 0.564 | 0.308 | 0.468 | 0.670 | 43 s, CPU |
| DistilBERT (fine-tuned) | 0.576 | 0.314 | 0.466 | 0.676 | ~14 min, GPU |
| kNN retrieval / Hybrid | *Phase 3* | | | | |

## 🔑 Key findings so far
**Data (Phase 1)**
- **82%** of products have only a short title (median 22 words).
- Only **~37%** of tag values are written explicitly in the text; grammar patterns alone recover **~10%** → motivates a **hybrid** of patterns, retrieval and LLM reasoning.
- Tags follow a **long tail** → report **macro-F1** as well as micro-F1.
- Similar texts share **~8× more tags** than random pairs → retrieval-based tagging is promising.

**Baselines (Phase 2)**
- After cleaning: **19,408 products, 214 descriptive tags, 2.26 tags/product**; split **by vendor** (no store in both train and test).
- Reading the text helps: both models are **~3× better** than predicting popular tags.
- **DistilBERT ≈ TF-IDF + LR** at ~20× the cost → the bottleneck is the **information in short titles**, not the text model.
- **Rare tags** (< ~150 train examples) often score F1 = 0; **Color** and **Skin Type** are the weakest attributes.
- **36%** of "wrong" predictions have their value written in the text → vendor tags are **incomplete**; measured scores are pessimistic.
- Error types to address: label noise, implicit claims, word confusion, **negation** ("No Clip"), cleaning leftovers.

## 📁 Structure
```
├── README.md
├── Phase1_NLP_Foundations.ipynb
├── Phase2_Baselines.ipynb
├── Phase3_Retrieval.ipynb
└── ...                       # one notebook per phase
```
*Data and model files are not included (size + dataset license); notebooks download the data and save outputs to Google Drive.*
