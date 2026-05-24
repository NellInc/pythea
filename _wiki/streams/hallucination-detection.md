# Hallucination Detection (Strawberry)

<!-- wiki:type = stream -->
<!-- wiki:scope = pythea -->
<!-- wiki:created = 2026-05-23 -->
<!-- wiki:updated = 2026-05-23 -->
<!-- wiki:status = active -->

## Summary

Pythea's Strawberry module detects procedural hallucination: cases where a model generates correct intermediate steps but routes to a wrong answer — the computation worked, the routing failed. The detection method scrubs cited evidence and measures confidence change; no change indicates confabulation. Pythea also includes the Thea API client and offline QMV probing tools.

## Core Detection Method

Name from the canonical example: asking a model to count r's in "strawberry." It enumerates them correctly mid-generation, then outputs "2." Evidence was present; routing failed. (`README.md`)

**Detection algorithm** (`src/pythea/hallucination_detector/`): scrub the cited evidence from context, re-query, measure confidence delta. Unchanged confidence = confabulation. Catches:
- RAG that retrieves but doesn't read
- Chain-of-thought that cites steps it ignored
- Self-verification that validates without checking
- Citation confabulation (decorative sources)

(`README.md`)

## CLI

```bash
python -m strawberry.factual_recall \
  --question "Which US senators from Minnesota graduated from Princeton" \
  --out report.json
```

(`README.md`)

## Three Tools in Pythea

| Tool | Purpose | Entry point |
|------|---------|-------------|
| Strawberry | Procedural hallucination detection | `src/pythea/hallucination_detector/` |
| Thea Client | Lightweight client for Thea Mini Reasoning API | `src/pythea/client.py` |
| Offline QMV | Permutation-mixture evaluation via Bernoulli first-token logprob probes | `src/pythea/offline/` |

(`README.md`, `src/pythea/` directory listing)

## QMV (Offline Probing)

Model-agnostic permutation-mixture evaluation. Key outputs: `q_bar` (mean score), `q_lo` (lower bound), `js_bound` (Jensen-Shannon bound). (`README.md`)

## Benchmarks

`benchmarks/benchmark_stark_prime.py` — STARK-Prime benchmark evaluation. (`benchmarks/` directory listing)

## Provenance

- Sources consulted: `README.md`, `src/pythea/` directory listing, `benchmarks/` directory listing
- Last verified against sources: 2026-05-23

## See Also

- [[pythea:findings/routing-vs-computation]] — synthesis of when routing fails
- [[mettle:systems/verification-suites]] — Suite 10 (novel reasoning) tests analogous failure modes
