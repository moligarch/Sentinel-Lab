# Milestones

## M4 - Systems & OS Foundations

- **Scope**: Go runtime & memory model; pprof/trace; OTel dashboards.across services.

- **Exit**:

  - `/api` skeleton merged; `health/metrics` live

  - Notes committed in `/docs/learning/` for OSTEP ch. 1–3

  - ADR-0001 tooling/structure accepted

**Typical labels**: `phase:P1`, `type:learn|build|doc`, `module:policy-orchestrator|event-bus`



## M8 - Concurrency & Perf (Go/Rust)

- **Scope**: OSTEP + CS:APP core chapters; Rust Axum API skeleton; end-to-end trace IDs across services.

- **Exit**:

  - `enrichment-services` handles 10–50k rps with p99 ≤ target

  - Perf report in `/docs/perf/REPORT.md` with flamegraphs

**Typical labels**: `phase:P2`, `type:build|doc|eval`, `module:enrichment-services|observability`



## M11 - Rhai Rules Engine v1 (+ ETW intro)

- **Scope**:  Rhai AST cache + hot-reload; minimal ETW consumer PoC.

- **Exit**:

  - `rule-engine` loads `rules/*.rhai`, passes golden tests

  - Engine limits enforced; sample rules mapped to ATT&CK

**Typical labels**: `phase:P3`, `type:build|doc`, `module:rule-engine|win-etw-agent`



## M14 - Edge Sensors (eBPF/XDP & ETW)

- **Scope**:  Linux XDP/uprobes; Windows ETW pipeline; secure update path.

- **Exit**:

  - `net-sensors` + `win-etw-agent` publish to bus; drop/latency baselined

  - Security checklist committed

**Typical labels**: `phase:P4`, `module:net-sensors|win-etw-agent`



## M17 - Data Platform & AIOps/MLOps

- **Scope**: NATS/Kafka; Feast feature store; MLflow; Great Expectations; SLOs.

- **Exit**:

  - One batch + one streaming job in CI; GX checks green

  - SLO dashboards for ingest/enrich/LLM

**Typical labels**: `phase:P5`, `module:event-bus|mlops-data|observability`



## M20 - LLM Serving, Eval & Hardening

- **Scope**: vLLM gateway; RAG; Ragas/LangSmith CI; guardrails.

- **Exit**:

  - Latency SLO met; eval report comparing models; incident playbooks

**Typical labels**: `phase:P6`, `module:llm-gateway|eval-harness`
