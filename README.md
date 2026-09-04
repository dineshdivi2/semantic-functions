# Semantic Functions

**When should a local or small language model replace a brittle heuristic, and when should it not?**

Most software functions should remain deterministic: authentication, arithmetic, transactions, permissions, and invariant enforcement need predictable behavior.

But many operational decisions are not fundamentally deterministic. They depend on meaning, context, and ambiguity:

- Is this support request about access, billing, or a product defect?
- Which candidate document best answers the question?
- Do two records refer to the same real-world entity?
- Which incoming task is genuinely urgent?
- Which workflow should receive an ambiguous request?

Today, these decisions are often implemented with keyword rules, thresholds, classifiers trained for one narrow distribution, or manually maintained taxonomies. This repository explores when **local language models and small language models (SLMs)** can provide a better decision layer—without turning ordinary software into an unbounded agent.

> The goal is not “LLMs replace functions.”  
> The goal is to identify **bounded semantic functions** where model-mediated judgment is measurably better than the alternatives.

## The framing

A semantic function maps structured input into a decision whose correctness depends partly on interpretation.

```text
input state
    ↓
semantic interpretation
    ↓
bounded decision
    ↓
deterministic action / validator / human review
```

Examples:

| Function | Conventional approach | Semantic alternative |
| --- | --- | --- |
| Route a support ticket | Keywords and rules | Local model predicts intent and confidence |
| Rank retrieved passages | BM25/cosine weights | Small model reranker |
| Match duplicate records | Exact/fuzzy fields | Model-mediated entity match with evidence |
| Prioritize cases | Hand-tuned score | Model assigns a rubric-grounded priority |
| Validate free text | Regex and fixed labels | Local model extracts structured claims |

The model does **not** own execution. It proposes or ranks a decision inside a constrained interface; deterministic systems enforce schemas, policies, validators, and approval thresholds.

## Decision boundary

The useful question is not “can a model do this?” It is:

```text
Is the semantic improvement worth the added latency, cost, uncertainty,
and operational complexity for this particular function?
```

A candidate function should be assessed across:

- **Semantic ambiguity** — does meaning and context matter more than literal tokens?
- **Outcome verifiability** — can the result be checked automatically, sampled, or reviewed?
- **Failure impact** — what happens if the model is wrong or uncertain?
- **Latency and cost** — can a local/SLM inference path fit the workload?
- **Data sensitivity** — is local inference necessary or beneficial?
- **Fallback quality** — can the system degrade safely to rules, retrieval, or humans?
- **Drift resistance** — will rules and thresholds become brittle as language or workflows change?

## Output patterns

A semantic function should produce more than an opaque answer:

```json
{
  "decision": "route_to_billing",
  "confidence": 0.86,
  "rationale": "The request concerns an invoice charge and renewal date.",
  "evidence": ["invoice #1028", "renewal on 15 May"],
  "policy_version": "routing-v1",
  "requires_review": false
}
```

This makes the result auditable, testable, and usable by deterministic downstream code.

## What belongs here

- Taxonomies of semantic versus deterministic functions
- Design patterns for local/SLM decision layers
- Evaluation harnesses comparing models, rules, classifiers, and retrieval
- Failure analysis: abstention, disagreement, calibration, and fallback
- Small, reproducible case studies in routing, ranking, matching, and prioritization

## What does not

- Replacing cryptography, authorization, accounting, or transactional state with a model
- Letting a model directly execute irreversible actions without deterministic controls
- Treating fluent rationales as evidence
- Benchmarking only accuracy while ignoring latency, calibration, cost, and failure modes

## Initial research questions

1. Which semantic functions benefit most from local or small models rather than rules or embeddings alone?
2. What is the smallest model that reaches a useful quality–latency–cost frontier?
3. When is a hybrid architecture—model plus validator, retrieval, or human review—better than either approach alone?
4. How should confidence, abstention, and disagreement determine escalation?
5. What evaluation suite makes a replacement claim credible?

## Roadmap

- [ ] Define a semantic-function taxonomy and delegatability rubric
- [ ] Build a common evaluation schema
- [ ] Add the first case study: intent routing with rules vs embeddings vs local SLM
- [ ] Measure quality, calibration, latency, cost, and fallback behavior
- [ ] Publish decision records and failure cases, not just aggregate scores

## Status

This is an early research repository. The first objective is to make the decision framework and evaluation discipline precise before claiming that any model should replace a production function.
