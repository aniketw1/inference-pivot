# Ray Serve Seminar — registered 2026-10-02

Seminar: "Design scalable distributed inference systems for production AI applications, from classic ML models to large language models."

Registered by the user on 2026-10-02. Seminar date: November 3 (time not provided — ask if a reminder is wanted).

## Topics covered
- Ray Serve applications: deployments, composition, load-aware request routing
- LLM endpoints with Ray Serve LLM on engines like vLLM and SGLang
- Inference engine optimization: KV cache, paged attention, prefix caching, continuous batching
- Scaling frontier models: KV-cache-aware routing, prefill/decode disaggregation, wide expert parallelism for MoE models
- Custom LLMs on Anyscale + integrating with agentic platforms (Claude Code, Cursor, Codex)
- Cost comparison: self-hosted GPUs vs subscriptions vs token-based API billing

## Why it fits the plan
- Prefill/decode disaggregation session ties directly to the open interconnect question flagged in the serving-stack digest (KV-bytes-per-request vs bandwidth).
- KV-cache-aware routing is directly relevant to the FireRouter-style router capstone in the curriculum.
- Ray Serve LLM on vLLM/SGLang complements Phase 1 (vLLM internals) and the stretch OSS fix target (vLLM/SGLang).
- Note: Ray Serve LLM is a vendor-flavored layer (Anyscale ecosystem); the curriculum stays engine-agnostic, this seminar is context, not sprint study material.

Filed alongside the serving-stack side track (~/workspace/inference-pivot/notes/), not sprint study material.
