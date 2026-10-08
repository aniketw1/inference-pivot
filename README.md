# Inference Pivot

Learning in public: my path to becoming an inference systems engineer.

**Status:** kicks off after Oct 10, 2026 (currently heads-down in an interview sprint).
**Proof-of-work target:** a FireRouter-style cache-aware model router over a benchmarked
vLLM serving lab, plus one real custom CUDA kernel.

## Phase 0 — Get a GPU box
Rent a single-GPU environment (e.g. 24GB RTX 4090 on RunPod / Vast.ai).

## Phase 1 — vLLM internals (1–2 weeks)
PagedAttention paper, Orca (continuous batching), vLLM source, `benchmark_serving.py`.
Reading companion: mini-sglang (github.com/sgl-project/mini-sglang) — readable,
type-annotated implementation of continuous batching, radix cache, chunked prefill,
overlap scheduling. Try the `MINISGL_DISABLE_OVERLAP_SCHEDULING=1` ablation.

## Phase 2 — Serving economics (1 week)
KV-cache math, Splitwise, DistServe, build a cost model.
Side thread: disaggregated prefill-decode and the interconnect question
(see notes/2026-10-02-serving-stack-digest.md).

## Phase 3 — PyTorch internals (1–2 weeks)
torch.compile / Inductor, CUDA graphs.

## Phase 4 — One real CUDA kernel (2–3 weeks)
GPU MODE lectures; a fused kernel checked in and profiled.
Context: FlashInfer kernel library (what SGLang builds on), autotuning per GPU.

## Phase 5 — Router capstone (2–4 weeks)
Cache-aware router over vLLM serving small + large models, evaluated on a
GSM8K/MMLU subset with quality-vs-cost numbers.

## Phase 6 — Stretch: OSS fix
A real fix in vLLM or SGLang. Candidate reading: mini-sglang PR 113
(decode batch request ordering across TP ranks), PR 103 (overlap-scheduling /
FlashInfer race in the offline benchmark).

## Side track — serving-stack changelog
Dated digests of framework releases, papers, and repo fixes, filed under
`notes/`. Context, not study material.
