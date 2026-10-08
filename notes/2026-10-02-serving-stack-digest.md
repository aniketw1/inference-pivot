# Serving-stack digest — 2026-10-02

Source: user-provided "AI News Flash"-style brief. Claims below are as provided,
not independently verified. Filed as a side track on top of the inference curriculum.

## SGLang 0.5.21 (reported shipped 2026-10-01)
- On-the-fly switching between prefill and decode for PD instances — reassign a
  node's role without restart. Operational win for disaggregated deployments.
- Rust-based prefix cache now on by default (replaces Python impl; latency-motivated,
  cache lookups are in the hot path).
- New v1 APIs: Decisions API, Score API (Score points at retrieval-augmented /
  inline relevance-scoring use cases).
- Model support: DeepSeek-V4.1 Flash, several diffusion models incl. Qwen-Image 2.1.
- Read: near-weekly release cadence — compare trajectories, not snapshots.

## Paper of the day — SparseSpec (MLSys 2026)
"Accelerating Large-Scale Reasoning Model Inference with Sparse Self-Speculative
Decoding." Draft-model-free speculative decoding: the target model drafts for
itself using sparse attention (subset of heads, reduced KV reads), then a standard
full-attention verification pass preserves correctness. Targets the
memory-bandwidth bottleneck in long-chain reasoning decode. Acceptance rate
depends on reasoning-trace predictability — structured CoT wins most.

## SGLang scheduler deep-dive (via mini-sglang)
Four additive components: continuous batching → radix cache (shared-prefix KV
reuse via radix tree) → chunked prefill (bounds peak memory for long prompts,
slight TTFT cost) → overlap scheduling (CPU prepares batch N+1 while GPU runs
batch N; ablation via `MINISGL_DISABLE_OVERLAP_SCHEDULING`).

## mini-sglang PRs worth reading
- PR 113: "Stabilize decode batch request order across TP ranks" — ranks must
  agree on decode-batch order or sharded activations combine incorrectly.
  Merged May 2026. Correctness fix that matters under tensor parallelism.
- PR 103: CUDA illegal memory access in the offline benchmark with overlap
  scheduling on — race in the FlashInfer integration (GPU ops issued before prior
  batch memory released). Closed/resolved.

## Disaggregated prefill-decode
DistServe (arxiv): split prefill (compute-bound) and decode (memory-bandwidth-bound)
onto separate GPUs; reported up to 7.4x more requests under same latency SLOs,
or 12.6x tighter SLOs vs co-located. Mooncake (Moonshot AI): adds a distributed
KV-cache pool over RDMA so decode nodes pull shared prefixes on demand.
Bottleneck: interconnect — KV tensors are GBs per request; PCIe/100Gbps Ethernet
transfer latency eats the gains; NVLink/RDMA help. SGLang 0.5.21's PD role
switching targets exactly this (rebalance instead of hard-partition).

## FlashInfer 0.7.0 (post-release 1, current stable per brief)
- Paged KV-cache attention refactor: unified plan/run contracts across backends.
- `BatchDecodeMlaWithPagedKVCacheWrapper` renamed to `mla.BatchMLAPagedAttentionWrapper`
  (old names likely to deprecate — track if building on FlashInfer directly).
- Autotuning of kernel configs per GPU / batch-size distribution.

## Open thread (user-flagged)
The interconnect question in disaggregated inference — kept coming up, to be
gone into. Core of it: KV-bytes-per-request vs interconnect bandwidth determines
whether disaggregation wins; compression/quantization of KV, GQA's smaller cache
vs MHA, and cache-pool amortization (Mooncake) are the levers.
