# Sentinel Lab - Deepen Understanding of Systems (OS, Architecture, Rust, Go, Rhai, LLM, AIOps/MLOps, Data Eng)

*A reading-first, build-as-you-go roadmap spanning (hopefully!) **18 months**. Each phase lists **subjects → mapped modules/services → resources** (Book → Blog/Docs → Video/Course). Every module is independently developable with mocks and contracts so you always see progress.*

---

## Project modules at a glance (independent & composable)
- **rust-sensor-kit** (Rust): cross-platform agents & libs (proc, file, net, FS watchers).
- **win-etw-agent** (C/C++/Rust): ETW providers/consumers for Windows signals.
- **net-sensors** (Go/Rust): packet taps (eBPF/XDP), flow summaries, DDoS heuristics.
- **event-bus** (NATS or Kafka): unified transport (topics/subjects), schemas, dead-letter.
- **rule-engine** (Rust + Rhai): hot-reloadable rules, AST cache, typed bindings, sandboxes.
- **enrichment-services** (Go): IP/PE/file enrichers; threat-intel adapters.
- **policy-orchestrator** (Rust/Axum): deploys rules/policies to agents; RBAC & audit.
- **llm-gateway** (vLLM/OpenAI-compatible): for triage, summarization, playbooks.
- **eval-harness** (Python): golden datasets, RAG/agent eval; CI checks.
- **observability** (OTel + Prometheus + Grafana): traces/metrics/logs, SLOs.
- **mlops/data** (Python + SQL): feature store, data validation, model registry & rollout.

**Independence contract:** every service exposes HTTP/gRPC + message contracts. Provide **mock publishers/consumers** and **fake stores** so any module can be built and tested in isolation.

---

## Phase timeline
- **Phase 1 (Months 1-4):** Systems mindset + Rust core + OS foundations
- **Phase 2 (Months 5-8):** Advanced Go + concurrency + perf tooling + hardware-aware design
- **Phase 3 (Months 9-11):** Rhai rules engine + plugin architecture (+ Win ETW intro)
- **Phase 4 (Months 12-14):** Sensors at the edge (eBPF/XDP, ETW pipelines)
- **Phase 5 (Months 15-17):** Data platform & messaging + AIOps/MLOps/Data Eng foundations
- **Phase 6 (Months 18-20):** LLM services, evaluation, hardening & SRE/MLOps in prod

> Suggested weekly cadence (adapt as needed): **60% reading/notes → 40% building**.

---

## Phase 1 - Systems mindset + Rust core (Months 1-4)
**Subjects**
- Operating systems foundations: **virtualization, concurrency, persistence** (processes, threads, CPU scheduling, VM, paging, filesystems).
- Hardware & architecture essentials: **CPU caches, memory hierarchy, ISA vs. micro-arch**, NUMA awareness (high level).
- Rust 2024 core: ownership/borrowing, lifetimes, traits; **async I/O (Tokio)**; testing & property tests; observability (tracing).

**Build (modules)**
- `rust-sensor-kit`: crate workspace + config + tracing + pluggable input/output traits.
- `policy-orchestrator` (skeleton): Axum service with `/rules` CRUD and `/ingest` (mock).
- `event-bus`: docker compose for NATS/Kafka + tiny pub/sub CLI.

**Deliverables**
- End-to-end hello-flow **with mocks**: sensor → bus → orchestrator.
- Benchmarks for message round-trip; structured logs; **trace IDs** threaded through services.

**Resources (Book → Blog/Docs → Video/Course)**
- **Books**: 
  - *Operating Systems: Three Easy Pieces* (Arpaci-Dusseau) - primary OS text.
  - *Computer Systems: A Programmer's Perspective (3e)* - hardware, linking, caches, concurrency from a programmer's view.
  - *The Rust Programming Language (3e)*; *Programming Rust (2e/3e)*; *Rust for Rustaceans* - language depth & idioms.
  - *The Linux Programming Interface* (Kerrisk) - system call & UNIX/Linux APIs reference.
  - *Rust Atomics and Locks* (Mara Bos) - low-level concurrency foundations.
- **Blog/Docs**: Rust book/docs; Tokio & Axum guides; Linux perf/strace quick refs.
- **Video (optional)**: Jon Gjengset **Crust of Rust** (ownership, lifetimes, async).

---

## Phase 2 - Advanced Go + concurrency + perf + hardware-aware design (Months 5-8)
**Subjects**
- Go runtime internals: goroutines, scheduler, GC; **Go Memory Model**; atomics & locks.
- Concurrency theory & hardware: **cache coherency, memory ordering, lock-free concepts**; contention & false sharing.
- Performance engineering: pprof/trace, heap/CPU profiling, **GOMEMLIMIT**; backpressure, graceful shutdowns.

**Build (modules)**
- `enrichment-services` (Go): robust clients (timeouts, retries), bulkheads & circuit breakers; backpressure to bus.
- Add **OpenTelemetry** tracing to Rust/Go services; Grafana/Prometheus dashboards for latency/capacity.

**Deliverables**
- Load test: 10-50k rps synthetic events through bus → enrichment; capture flamegraphs + traces; fix one bottleneck and document.
- Reliability demo: rolling restart with zero data loss; chaos test (kill pod / network jitter) and report.

**Resources (Book → Blog/Docs → Video/Course)**
- **Books**: 
  - *The Go Programming Language* (Donovan & Kernighan).
  - *Concurrency in Go* (Katherine Cox-Buday).
  - *The Art of Multiprocessor Programming (Revised)* (Herlihy & Shavit) - deep concurrency theory.
  - *Computer Architecture: A Quantitative Approach (6e)* (Hennessy & Patterson) - caches, ILP, memory systems.
  - *Systems Performance (2e)* (Brendan Gregg) - OS+HW perf methodology.
- **Blog/Docs**: Effective Go; **Go Memory Model**; Go pprof & runtime/trace guides; OTel instrumentation.
- **Video (optional)**: ArdanLabs **Ultimate Go** concurrency sections.

---

## Phase 3 - Rhai rules engine + plugins (+ Win ETW intro) (Months 9-11)
**Subjects**
- **Rhai** scripting language: AST, sandboxes, scopes, type APIs, plugin modules.
- Embedding Rhai in Rust: function registration, fallible APIs, script caps.
- DSL thinking: rule shapes, policies, schema versioning, hot reload, caching AST.
- Windows signals 101: ETW basics; event providers & consumers; WPA/WPR toolchain.

**Build (modules)**
- `rule-engine` (Rust + Rhai):
  - Rhai API surface (context object, emit(), enrich(), matchers).
  - AST cache; hot reload; capability flags; resource limits.
  - Testkit: golden tests for rules with fixed inputs/outputs.
- `win-etw-agent` (skeleton): subscribe to 1-2 providers; forward to bus (dev channel).

**Deliverables**
- Sample rules pack (proc-spawn anomaly, suspicious DNS, file write patterns).
- Fuzz tests on parser/bindings; sandboxing limits proven by tests.

**Resources**
- **Books**: *Domain-Specific Languages* (Fowler); *Language Implementation Patterns* (Parr); *(Windows) Windows Internals, Part 1 (7e)*.
- **Blog/Docs**: *The Rhai Book*; Rhai plugin modules; Microsoft ETW docs; WPA/WPR primers.
- **Video/Course** (optional): Pavel Yosifovich intro to WPA/WPR.

---

## Phase 4 - Sensors at the edge (eBPF/XDP, ETW pipelines) (Months 12-14)
**Subjects**
- Linux telemetry with **eBPF**: XDP, kprobes/uprobes, ring buffers, verifier constraints.
- Go/Rust eBPF stacks (cilium/ebpf; Aya); portable builds; CO-RE basics.
- Event shaping & sampling; memory safety at boundaries; secure update channels.

**Build (modules)**
- `net-sensors`:
  - XDP packet counters + flow summaries (Go+cilium/ebpf or Rust+Aya).
  - Ship metrics/events to `event-bus`; backpressure strategy.
- `win-etw-agent`:
  - Add channels/filters; bundle WPA-recorded repro traces for tests.

**Deliverables**
- Latency & drop-rate benchmarks under load; kernel/user copies minimized.
- Security review checklist (capabilities, seccomp, kernel pinning, code-signing plan).

**Resources**
- **Books**: *Learning eBPF* (Rice); *BPF Performance Tools* (Gregg).
- **Blog/Docs**: cilium/ebpf docs; Aya book & crate docs; ebpf.io; ETW/WPA docs.
- **Video/Course** (optional): eBPF Summit talks on XDP & observability.

---

## Phase 5 - Data platform & messaging + AIOps/MLOps/Data Eng (Months 15-17)
**Subjects**
- **Messaging**: NATS vs. Kafka semantics; at-least/exactly-once; partitions/subjects.
- **Data Engineering**: batch + streaming, lake vs. OLTP, schemas & contracts, CDC.
- **AIOps/MLOps** foundations: SLOs & error budgets; feature stores; data validation; model registry; canary & shadow deployments; offline/online parity.

**Build (modules)**
- `event-bus`: choose NATS *or* Kafka, define subjects/topics, schemas (Avro/Protobuf/JSON-Schema).
- `mlops/data`: 
  - Feature store (Feast) for signals (proc, net, file).
  - Data validation (Great Expectations) on raw & curated datasets.
  - Experiment tracking & model registry (MLflow).
- `observability`: service SLOs, dashboards, alert routes; synthetic probes.

**Deliverables**
- Reproducible local stack with docker compose; make target `make up && make demo`.
- One offline job + one streaming job to materialize features; data quality gates in CI.

**Resources**
- **Books**: *Designing Data-Intensive Applications* (Kleppmann); *Fundamentals of Data Engineering* (Reis & Housley); *Streaming Systems* (Akidau et al.); *Kafka: The Definitive Guide (2e)* (Shapira et al.); *Designing Event-Driven Systems* (Stopford); *(SRE) Site Reliability Engineering + Workbook* (Google).
- **Blog/Docs**: NATS docs; Kafka intro & quickstart; Feast quickstart; Great Expectations intro/quickstart; MLflow docs (incl. GenAI/Tracing); SRE workbook chapters on SLOs.
- **Video/Course** (optional): Confluent "Intro to Kafka"; O'Reilly talks for FDE; Google SRE videos.

---

## Phase 6 - LLM services, evaluation & hardening (Months 18-20)
**Subjects**
- LLM serving at the edge: **vLLM** OpenAI-compatible server; request tracing; cost & latency budgets; safety filters.
- RAG/agent evaluation: datasets, metrics (faithfulness, context precision/recall), eval-driven development with **Ragas** or **LangSmith**.
- Production readines: tenancy, quotas, rate limits; secrets & keys; rollout (blue/green, canary); incident playbooks.

**Build (modules)**
- `llm-gateway`: proxy to vLLM; auth, logging, quotas; tool/function-calling adapter.
- `eval-harness`: regression suites; nightly offline eval; dashboards.
- Integrate with `policy-orchestrator` to allow rules to call LLM tools (summarize, enrich).

**Deliverables**
- Latency SLO met under N concurrent requests; eval report comparing 2-3 models/params.
- Postmortem template; chaos experiment (kill pod, degrade network) with auto-recovery.

**Resources**
- **Books**: *Natural Language Processing with Transformers* (HF/O'Reilly); *Designing Machine Learning Systems* (Chip Huyen); *Machine Learning Design Patterns* (Lakshmanan et al.); *Reliable Machine Learning* (O'Reilly).
- **Blog/Docs**: vLLM OpenAI-compatible server & quickstart; PEFT/QLoRA docs; Ragas & LangSmith evaluation guides; OpenTelemetry traces for LLMs.
- **Video/Course** (optional): Hugging Face LLM Course; LangSmith evaluation tutorials.

---

## Cross-cutting "AIOps/MLOps/Data Eng" checklist (keep updating)
- **Data**: raw/bronze → clean/silver → feature/gold; schemas; retention; PII handling.
- **Reliability**: SLOs for ingest, enrichment, LLM; error budgets; load tests; autoscale.
- **Quality**: Great Expectations suites in CI; drift monitors (Evidently) in prod.
- **Experimentation**: MLflow tracking; lineage; model registry with staged rollout.
- **Observability**: unified tracing (OTel) across agents → bus → services → LLM.
- **Security**: signing & provenance for agent updates; role-scoped keys; secret scans.

---

## Milestones snapshot
- **M4:** Rust services skeleton + bus + first end-to-end trace/metrics.
- **M8:** Go enrichers at scale + dashboards + perf report.
- **M11:** Rhai rule engine v1 + ETW ingest proof + rules testkit.
- **M14:** eBPF/XDP sensor + ETW pipeline + secure update channel.
- **M17:** Data platform online (Feast/MLflow/GX) + SLOs + CI gates.
- **M20:** vLLM-backed gateway + evaluation harness + incident playbooks.

---

## How to run locally (one command per module)
Each repo exposes `make dev` with mocks for its upstream deps. Example order:
1) `docker compose up` (bus, postgres, grafana)
2) `observability`
3) `policy-orchestrator`
4) `enrichment-services`  
5) `rust-sensor-kit`  
6) `rule-engine`  
7) `net-sensors`  
8) `win-etw-agent`  
9) `llm-gateway` 
10) `eval-harness`.

> Tip: Keep a `/contracts` folder (JSON-Schema/Protobuf) versioned and tested.

---

### Notes on study style
- Your stated preference is **Book → Blog → Video/Course**. Lists above honor that order.
- You're a fast reader-bias for primary sources and specs; videos are optional.

---

### Stretch ideas (optional)
- Stateless vs. stateful rule runs with windowed state (Ring buffer + Rhai API).
- Rule-pack marketplace format (signing; capability manifest). 
- Agent auto-upgrade with staged rollouts and canaries.
- Cost dashboards for LLM & storage per tenant.

