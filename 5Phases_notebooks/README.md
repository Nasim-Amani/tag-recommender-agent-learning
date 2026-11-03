# 🏷️ Tag Recommender Agent: Learning Repo

From NLP basics to an **LLM-based agent that recommends product tags** from vendor text.
Companion to my research proposal: *autonomous tag recommendation using **syntactic patterns** and **neighbouring tag attributes***.

**Data:** [Amazon Reviews 2023](https://amazon-reviews-2023.github.io/), *All_Beauty* metadata: 19,408 products · 214 tags · 2.26 tags/product · split **by vendor** (no leakage)
**Background:** time-series forecasting (LSTM, Transformer) → NLP, recommender systems, AI agents

## 🗺️ Roadmap
| # | Topic | Key methods | Notebook | Status |
|---|---|---|---|---|
| 1 | NLP foundations | TF-IDF, embeddings, spaCy syntactic patterns | [Phase1](Phase1_NLP_Foundations.ipynb) | ✅ |
| 2 | Baselines | Multi-label, vendor split, TF-IDF + LR, DistilBERT | [Phase2](Phase2_Baselines.ipynb) | ✅ |
| 3 | Retrieval | MiniLM + FAISS, kNN tag propagation, hybrid | [Phase3](Phase3_Retrieval.ipynb) | ✅ |
| 4 | Tag graphs | PMI, communities, graph re-ranking, conflicts | [Phase4](Phase4_Tag_Graphs.ipynb) | ✅ |
| 5 | LLMs | Qwen2.5: zero/few-shot, JSON, RAG, abstention, LoRA | [Phase5](Phase5_LLMs.ipynb) | ✅ CPU run · ⏳ GPU run |
| 6 | Agent | Tools, verifier, memory, LLM on uncertain tags, ReAct | [Phase6](Phase6_Agent.ipynb) | 🟡 |
| 7 | Experiments & paper | Ablations, cross-domain, error analysis, cost | — | ⬜ |

## 🏁 Results (test set, vendor split)
| Model | micro-F1 | macro-F1 | P@3 | MAP |
|---|---|---|---|---|
| Popularity (no text) | 0.152 | 0.007 | 0.134 | 0.198 |
| **TF-IDF + LR** | 0.579 | 0.306 | 0.463 | 0.683 |
| DistilBERT | **0.589** | **0.318** | 0.463 | 0.686 |
| kNN (TF-IDF / MiniLM) | 0.541 / 0.526 | 0.269 / 0.266 | 0.430 / 0.418 | 0.634 / 0.613 |
| Hybrid (LR + kNN) | 0.580 | 0.304 | **0.465** | **0.687** |
| + tag graph | no change (best β = 0) | | | |

➡️ **Ceiling at P@3 ≈ 0.46:** every method learns from the **same short vendor titles**, so they mostly repeat what TF-IDF + LR already knows.

## 🔑 Key findings
**1 · Data**
- 82% of products have **only a short title**. Only ~37% of tag values are **written in the text** → most tags are **implicit**.
- Long-tail tags → report **macro-F1** too.

**2 · Baselines**
- Text helps: ~3× better than popularity.
- **DistilBERT ≈ LR at ~20× the cost** → the bottleneck is **information**, not model power.
- 36% of "wrong" predictions are in the text → **vendor tags are incomplete** (missing labels).

**3 · Retrieval**
- kNN < LR; **words beat meaning** for finding neighbours. Hybrid ≈ LR.
- Helps on **rare tags** (+11% F1), not on implicit ones. A random split inflates scores (27.8% same-vendor neighbours).

**4 · Tag graph**
- Finds **6 product families** from co-occurrence alone (211 tags, 1,038 edges).
- Re-ranking doesn't help: the graph knows **which attribute**, not **which value** → useful for **explanations and conflict checks**.

**5 · LLMs** *(CPU learning run: 0.5B model, 30 products → concepts only, not final numbers)*
- Design: LR proposes **15 candidates** → the LLM chooses → **JSON** + defensive parser.
- The small model **over-tags**: it picks ~12 of 15 candidates (truth ≈ 2.4) → high recall, low precision.
- **Read recall together with avg tags**. LLM "implicit tag" gains can just come from choosing many tags.
- A **strict prompt** acts like a threshold (precision ↑, recall ↓), but the model **never abstains** by itself.
- Cost: **10–30 s/product** on CPU vs < 0.001 s for LR.
- ⏳ Final run: Qwen2.5-1.5B on GPU, 500 products, + LoRA.

**6 · Agent (in progress)**
- Workflow: **tools** (LR · spaCy · similar products · tag graph · vendor memory) → **evidence table** → learned **verifier** → ✅ accept / ❌ reject / ❓ uncertain → **LLM only for ❓** → **abstain** if nothing is confident.
- Direct test of the proposal: do **syntax** and **neighbouring-tag** evidence add information beyond LR?

## 📁 Structure
```
├── README.md
├── Phase1_NLP_Foundations.ipynb
├── Phase2_Baselines.ipynb
├── Phase3_Retrieval.ipynb
├── Phase4_Tag_Graphs.ipynb
├── Phase5_LLMs.ipynb
└── Phase6_Agent.ipynb
```
*Data and models are not included (size + license). The notebooks download the data and save outputs to Google Drive.*
