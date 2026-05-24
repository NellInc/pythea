# Routing vs. Computation Failure Modes

<!-- wiki:type = finding -->
<!-- wiki:scope = pythea -->
<!-- wiki:created = 2026-05-23 -->
<!-- wiki:updated = 2026-05-23 -->
<!-- wiki:status = active -->

## Summary

Pythea's central finding is a taxonomy of failure modes where computation succeeds but output routing fails. The model "knows" the answer — it appears in intermediate output — but the final answer does not use it. This is distinct from factual ignorance and requires different detection methods.

## Failure Mode Taxonomy

All from `README.md`:

1. **RAG retrieval without reading** — document retrieved, not incorporated into answer generation
2. **Chain-of-thought citation without use** — CoT cites a reasoning step; final answer ignores it
3. **Self-verification without checking** — model reports "I verified X"; verification did not occur
4. **Citation confabulation** — sources cited decoratively, not actually used

**Common structure**: the evidence exists in the generation context; the routing from evidence to answer is broken.

## Detection Method

Scrub the cited evidence from context; re-query; measure confidence delta. (`README.md`)
- Large confidence drop → evidence was genuinely used
- Small or no confidence drop → evidence was confabulated or routed past

This is the Strawberry module's core algorithm (`src/pythea/hallucination_detector/`).

## QMV Probing as Complement

Offline QMV (permutation-mixture evaluation) provides a model-agnostic measure of output stability under permutation. Where Strawberry tests evidence-use specifically, QMV tests broader output consistency. Together they cover different routing failure surfaces. (`README.md`, `src/pythea/offline/`)

## Relationship to METTLE

METTLE Suite 10 (Novel Reasoning) tests analogous substrate: iteration curves reveal whether a model's reasoning actually drives output improvement. Both pythea and METTLE probe the gap between process and output. (`README.md`, METTLE `README.md`)

## Provenance

- Sources consulted: `README.md`, `src/pythea/` directory listing
- Last verified against sources: 2026-05-23

## See Also

- [[pythea:streams/hallucination-detection]] — the detection toolkit
- [[mettle:systems/verification-suites]] — Suite 10 tests related failure modes
