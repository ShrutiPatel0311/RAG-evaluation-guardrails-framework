
# RAG Evaluation & Guardrails Framework

A retrieval-augmented generation (RAG) pipeline paired with an automated
evaluation harness and a guardrail layer — built to answer not just "does it
work?" but "how do I know it works, and what happens when someone tries to
break it?"

Most RAG demos stop at retrieve-and-generate. This project treats evaluation
as a first-class component: it measures retrieval quality, scores how
faithfully generated answers are grounded in retrieved context, and
stress-tests the system against adversarial inputs like prompt injection —
then reports a clear before/after comparison once guardrails are added.

## Why this project

Retrieval-augmented generation is the default way most teams ship LLM
features today, but "it gave a reasonable-looking answer" isn't the same as
"it's reliable." This project builds the harness that tells the difference,
using the same benchmarks and techniques (RAGAS, RAGTruth, BEIR, TruthfulQA)
that production ML teams use to evaluate RAG systems.

## Architecture

```
            INDEXING                              QUERY
  documents → chunk → embed → vector DB     user query → embed query
                                                  ↓
                                          retrieve top-k chunks
                                                  ↓
                                   [guardrail: scan for injected instructions]
                                                  ↓
                                      LLM generates cited answer
                                                  ↓
                                 [guardrail: reject if faithfulness < threshold]
                                                  ↓
                                              final answer
```

## Tech stack

| Component | Tool |
|---|---|
| Embeddings | sentence-transformers (all-MiniLM-L6-v2) |
| Vector store | ChromaDB (local, persistent) |
| Generation | Claude API |
| Retrieval eval | BEIR, HotpotQA |
| Faithfulness eval | RAGAS, RAGTruth |
| Guardrail eval | TruthfulQA, HackAPrompt |
| Dashboard | Streamlit |

## Status
- [x] Phase 1: Baseline RAG pipeline (ingest, chunk, embed, retrieve, generate)
- [ ] Phase 2: Retrieval quality evaluation (BEIR / HotpotQA)
- [ ] Phase 3: Faithfulness scoring (RAGAS / RAGTruth)
- [ ] Phase 4: Guardrails (TruthfulQA / prompt injection)
- [ ] Phase 5: Dashboard

## Results

_Filled in as each phase completes — this is the section that matters most
for a resume or interview, since it's the evidence behind the claims above._

| Metric | Baseline | With guardrails |
|---|---|---|
| Retrieval recall@5 | TBD | — |
| Faithfulness (RAGAS) | TBD | TBD |
| Hallucination rate vs. RAGTruth baseline | TBD | TBD |
| TruthfulQA pass rate | TBD | TBD |
| Prompt-injection block rate | — | TBD |

## Setup

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env             # then paste your Anthropic API key into .env
```

## Usage

1. Add your source documents as `.txt` files to `data/corpus/` (a few sample docs
   are included so you can test the pipeline immediately — replace them with your
   real corpus: a Wikipedia subset, your field's arXiv papers, etc.)
2. Build the index:
   ```bash
   python src/ingest.py
   ```
3. Ask a question:
   ```bash
   python src/query.py "your question here"
   ```

## Next steps (hand these to Claude Code)

See `NEXT_PROMPTS.md` for the ready-to-paste prompts for phases 2 through 5.

## What this demonstrates

- End-to-end RAG system design (chunking, embedding, retrieval, generation)
- Rigorous evaluation methodology rather than anecdotal "it seems to work"
  testing, using established benchmarks (BEIR, HotpotQA, RAGTruth, TruthfulQA)
- Security-mindedness: adversarial testing against prompt injection, a
  real-world LLM vulnerability
- Ability to quantify engineering trade-offs with a clear before/after
  comparison

## Project structure

```
rag-eval-guardrails/
├── data/corpus/        # your source documents go here
├── src/
│   ├── ingest.py        # chunk, embed, store in ChromaDB
│   └── query.py         # CLI: retrieve + generate an answer
├── results/              # eval outputs will land here (phase 2+)
├── requirements.txt
└── .env.example
```
