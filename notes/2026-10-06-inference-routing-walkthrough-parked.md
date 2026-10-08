# Inference routing walkthrough — parked for post-Oct-10 bundle

Parked: 2026-10-06. Deliver after the Oct 10 Google interview, bundled with the DeepInfra $100M ARR item as a post-sprint "inference side-track" reading bundle.

## Source
- Title: Scale LLM inference with smarter routing and KV cache
- Link: https://pub.towardsai.net/stop-buying-gpus-scale-llm-inference-with-smarter-routing-and-kv-cache-management-785c6be30911
- Published: 2026-10-06 (feed item, not independently opened — claims self-consistent as a bookmark)

## Two-sentence summary for delivery
Prefill hogs compute while decode hogs memory bandwidth, so running both on the same GPUs wastes the hardware you already own. The walkthrough makes cache-aware routing the first fix before buying more cards, with a Llama 3 70B example where a single 9,000-token request burns 2.95 GB of KV cache alone.

## Why it fits the thread
Directly on the inference pivot: the 3-month mini-inference-engine goal, the endorsed FireRouter-style cache-aware router capstone, and the flagged "interconnect question" (KV bytes per request vs bandwidth) — the 2.95 GB datapoint is concrete fuel for that question.
