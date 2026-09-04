# Case Study 01: Local/SLM Reranking

## The first semantic function

Ranking is the repository's first concrete semantic function.

A conventional retrieval pipeline returns candidates using lexical, dense, or hybrid retrieval:

```text
query
  ├── BM25 / lexical retrieval
  ├── dense-vector retrieval
  └── fusion (optional)
          ↓
      20–100 candidates
          ↓
   local or small-model reranker
          ↓
      best K results
```

The reranker makes a bounded relevance judgment: *given this query and a candidate passage, how useful is the passage for the task?* It does not choose tools, alter data, or execute an action.

## Why this is a good first case

A small/local model can use semantic cues that fixed lexical weights and embedding similarity often miss:

- an answer is relevant despite different terminology
- a passage matches the question but lacks an exact keyword
- an exact keyword match is misleading
- one result is more directly useful than another

The claim to test is not that an LLM always beats BM25, dense search, or hybrid retrieval. The claim is narrower:

> For a defined corpus, query distribution, model, and latency budget, can a local/SLM reranker improve the quality of the final ranked list?

## Baselines and ablations

Every experiment should compare a final ranking, not a vague architecture:

| Method | Purpose |
| --- | --- |
| BM25 | Lexical baseline |
| Dense retrieval | Semantic-vector baseline |
| Hybrid retrieval | Candidate-recall baseline |
| RRF / weighted fusion | Fusion baseline |
| Local/SLM reranker | Model-mediated ranking decision |
| Reranker + abstention/fallback | Production-oriented variant |

The model should rerank the same candidate set when comparing rankers. Otherwise, a candidate-generation improvement can be confused with a ranking improvement.

## Evaluation

The final ranking is evaluated with standard retrieval metrics:

| Metric | Question |
| --- | --- |
| Recall@K | Did the candidate set contain the relevant evidence? |
| MRR | How early did the first relevant result appear? |
| nDCG@K | Did the final order place the most useful evidence near the top? |

For hybrid retrieval, add diagnostics:

- **Unique contribution:** relevant results found only by BM25, only by dense retrieval, or by both
- **Fusion quality:** does RRF or weighted fusion improve the final ranking?
- **Query slices:** exact-identifier, semantic, and mixed queries
- **Failure cases:** query types where reranking lowers nDCG or misses a key exact match

## Production constraints

A viable local reranker needs more than quality:

- bounded input length and candidate count
- predictable latency at the target hardware and concurrency
- structured score/reason output where needed
- confidence or margin-based abstention
- deterministic fallback to the original ranking
- versioned prompts, model weights, datasets, and evaluation results

## Initial hypothesis

A local or small reranker is most likely to help when candidate recall is already good but ranking quality is poor—for example, when the relevant passages are present in the top 20 but buried below superficially similar results.

That distinction matters:

```text
retrieval improves coverage
reranking improves order
evaluation must measure both
```
