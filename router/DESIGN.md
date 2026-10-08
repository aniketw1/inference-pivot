# Fireworks-style inference router — design (in progress)

## Goal
Route each incoming request to the cheapest model that meets the quality bar.
Small model for the easy 80%, large model for the hard 20% — with prefix-cache
awareness so repeated context (system prompts, few-shot examples, conversation
history) keeps hitting warm KV cache instead of recomputing prefill.

Inspiration: Fireworks' FireRouter (cache-aware routing across a model fleet).

## Architecture

```
clients → router → vLLM (small model, e.g. 4B-class) ─┐
                   → vLLM (large model, e.g. 30B-class) ┘
```

- Both models served with vLLM (continuous batching, PagedAttention, prefix caching on).
- Router is a standalone process in front: classifies each request, picks a backend,
  forwards, and records outcome metrics.
- v1: heuristic policy (request complexity signals → model choice) + sticky routing
  on conversation/session id for cache hits.
- v2: cache-aware policy — estimate prefix-cache hit rate per backend, route to
  maximize expected hits; fall back to large model on low confidence.

## What "cache-aware" means concretely
vLLM's prefix caching stores KV blocks keyed by token prefix. If many requests
share a system prompt, routing them to the same backend turns prefill from
O(n) recompute into a cache lookup — this is where TTFT drops. The router's job
is to keep shared prefixes co-located while still load-balancing.

## Eval plan
- Datasets: GSM8K and MMLU subsets (small enough to run on a single GPU box).
- Metrics per policy: accuracy, cost per 1k requests (GPU-seconds), p50/p99 TTFT
  and TPOT, prefix-cache hit rate.
- Baselines: small-only, large-only, random routing. The router must beat
  small-only on quality and large-only on cost — otherwise it's theater.

## Milestones
1. GPU box + vLLM serving both models, `benchmark_serving.py` numbers captured.
2. Router v1 (heuristic + sticky sessions) in front; correctness first.
3. Cache-aware policy v2; measure hit-rate lift.
4. Full eval: quality-vs-cost curves published in this repo.

## Open questions
- How to estimate request difficulty cheaply before routing? (classifier? token-count heuristics? tiny proxy model?)
- When the small model is wrong, is retry-on-large cheaper than routing right the first time?
- Prefix-cache hit rate vs load balance: what's the right tradeoff knob?
