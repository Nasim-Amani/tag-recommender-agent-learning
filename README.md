# 🏷️ Tag Recommender Agent: Learning Journey

A step-by-step learning repo: from NLP basics to an **LLM-based agent that recommends product tags** from vendor text.
Built alongside my research proposal on *autonomous tag recommendation using syntactic patterns and neighbouring tag attributes*.

**Background:** time-series forecasting (LSTM, Transformer) → learning NLP, recommender systems and AI agents.
**Data:** [Amazon Reviews 2023](https://amazon-reviews-2023.github.io/) item metadata, category *All_Beauty* (vendor text → tags from the `details` attributes).

## 🗺️ Roadmap (16 weeks)

| Phase | Weeks | Topic | What I learn / build | Notebook | Status |
|---|---|---|---|---|---|
| 1 | 1–3 | **NLP foundations** | Text cleaning, tokenization, TF-IDF, embeddings, spaCy POS & dependency parsing, syntactic patterns | [Phase1_NLP_Foundations](Phase1_NLP_Foundations.ipynb) | ✅ Done |
| 2 | 3–5 | **Recommenders & multi-label** | Multi-label classification, leak-free vendor split, metrics (P@k, F1, MAP), baselines: TF-IDF + LR, DistilBERT | [Phase2_Baselines](Phase2_Baselines.ipynb) | 🟡 In progress |
| 3 | 5–6 | **Embeddings & retrieval** | sentence-transformers + FAISS, kNN tag propagation | — | ⬜ |
| 4 | 6–8 | **Tag graphs** | Tag co-occurrence graph (PMI), graph re-ranking, optional GCN | — | ⬜ |
| 5 | 8–10 | **LLMs** | Prompting, structured JSON output, RAG, LoRA fine-tuning, abstention | — | ⬜ |
| 6 | 10–12 | **Agent** | Tool calling, ReAct loop, verifier, memory → end-to-end tag agent | — | ⬜ |
| 7 | 12–16 | **Experiments & paper** | Ablations, cross-domain tests, error analysis, cost/latency, write-up | — | ⬜ |

## 🔑 Key findings so far
- **82%** of products have only a short title (median 22 words).
- Only **~37%** of tag values are written explicitly in the text; grammar patterns alone recover **~10%** → motivates a **hybrid** of patterns, retrieval and LLM reasoning.
- Tags follow a **long tail**: the top 21 of 298 tags cover 50% of all tag uses → report **macro-F1** as well as micro-F1.
- Similar texts share **~8× more tags** than random pairs → retrieval-based tagging is promising.

## 📁 Structure
```
├── README.md
├── Phase1_NLP_Foundations.ipynb
├── Phase2_Baselines.ipynb
└── ...                       # one notebook per phase
```
*Data and model files are not included (size + dataset license); notebooks download the data and save outputs to Google Drive.*
