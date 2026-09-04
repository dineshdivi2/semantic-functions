# Delegatability Rubric

A language model should not be selected because it can produce a plausible answer. It should be selected only when its judgment can be bounded, evaluated, and safely integrated.

## Function categories

| Category | Default implementation |
| --- | --- |
| Deterministic invariants: arithmetic, cryptography, authorization, transactions | Code |
| Semantic judgment with easy verification: routing, ranking, extraction | Local/SLM + validator |
| Semantic judgment with costly or partial verification: prioritization, matching | Model + review threshold |
| High-impact irreversible decisions | Human approval plus deterministic policy |
| Open-ended exploration | Model, with explicit limits and no direct execution |

## Assessment dimensions

Score each candidate function before building it:

1. **Ambiguity** — Does correctness depend on natural-language meaning or contextual interpretation?
2. **Verifiability** — Can a result be automatically checked, reviewed, or evaluated against labelled data?
3. **Failure impact** — Is an incorrect decision reversible and contained?
4. **Latency budget** — Can local inference meet the operational response time?
5. **Cost and scale** — Is the small-model path viable at expected volume?
6. **Data sensitivity** — Does local deployment reduce meaningful privacy or governance risk?
7. **Fallback** — Is there a safe rule, ranked list, or human path when confidence is low?
8. **Drift** — Are fixed rules likely to decay as language, users, or workflows change?

A function with high ambiguity but low verifiability or high failure impact is usually a poor candidate for autonomous delegation.

## Contract

A semantic function should expose a typed contract:

```text
input → decision + confidence + evidence + policy version → deterministic action or escalation
```

The model supplies bounded interpretation. The surrounding system owns authorization, validation, persistence, and execution.
