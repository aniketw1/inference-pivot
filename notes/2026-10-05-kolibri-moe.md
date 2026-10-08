# Kolibri (Aleph Alpha) — 78B MoE open weights, 3.46B active/token — 2026-10-05

Source: feed item (MarkTechPost summary of Aleph Alpha release), not independently
verified. Filed as side track on top of the inference curriculum.

## The headline
Aleph Alpha released **Kolibri**: open-weight English–German MoE, 78.1B total
parameters, only **3.46B active per token**. Serves through **vLLM on a single
H200**, with per-request reasoning effort and a dedicated tool-call parser
built in.

## Why it matters for the curriculum
- **Phase 1 (vLLM internals):** a concrete reference MoE that runs on one H200
  under vLLM. Worth tracing how vLLM's expert-parallelism / all-to-all
  dispatch handles 3.46B active — MoE serving is the hard case for the
  scheduler and the interconnect.
- **Phase 2 (serving economics):** 78B-on-paper / 3.46B-in-practice is the
  extreme version of the cost-per-token question: pricing follows active
  params, capability follows total params. MoE turns the per-token cost curve
  into the product pitch.
- **Reasoning effort as a serving knob:** per-request reasoning effort means
  the decode length is demand-driven — ties directly to the SparseSpec thread
  (acceptance rate depends on trace predictability; variable effort ⇒ variable
  speedup). Also a scheduler wrinkle: mixed-effort requests in one batch.
- **Built-in tool-call parser:** structured-output parsing moved into the
  serving stack rather than the app layer — relevant to the work-build
  (self-improving agent loop) as much as the curriculum.

## Open threads
- Expert routing at 3.46B active: how balanced is expert utilization, and does
  the all-to-all still fit comfortably inside one H200 (NVLink-free)?
- EN–DE bilingual: Aleph Alpha is German; worth noting for the Germany-emigration
  thread only as "EU frontier-lab option exists" — no application intent.
