# Evaluation Principles

## A replacement claim requires an ablation

The question is never simply “did the model score well?” Compare it with the alternatives it is meant to replace:

```text
rule / heuristic
embedding or classifier
retrieval or fusion baseline
local / small-model decision layer
model + validation / fallback
```

For ranking, measure Recall@K, MRR, and nDCG@K. For routing or matching, measure task-specific quality alongside calibration, abstention, latency, cost, and the impact of errors.

## Evaluate slices, not only averages

Aggregate scores hide the reason a system works or fails. Split results by cases such as:

- exact identifiers and rare terms
- semantically phrased questions
- mixed lexical and semantic requests
- ambiguous or underspecified input
- adversarially similar candidates
- low-confidence decisions and fallbacks

## Version the experiment

Record the dataset, split, labels, baseline configuration, model and prompt version, hardware, candidate-set policy, and result artifacts. A semantic function is a production component only when its behavior can be re-run, compared, and challenged.
