# SOC Triage Agent — an open-source LangGraph service for agentic SOC alert triage

> Case study by Ansh Saxena (Backend & ML Infrastructure Engineer at Cyware Labs, Bengaluru) of SOC Triage Agent, an open-source agentic security-alert triage service built with Python, FastAPI, LangGraph and Pydantic. Plain Python makes every structural decision (routing, time windows, tool budgets); the LLM only reasons about meaning.

Canonical page: https://anshsaxena05.github.io/projects/soc-triage-agent.html
Author: [Ansh Saxena](https://anshsaxena05.github.io/) (GitHub: AnshSaxena05)
Source code: https://github.com/AnshSaxena05/cyberSecurity_alert_triage

SOC Triage Agent is an open-source service by [Ansh Saxena](https://anshsaxena05.github.io/). It ingests alerts from Splunk, CrowdStrike, AWS GuardDuty and other sources, normalises them, and enriches them through MITRE-routed tools. It then produces a structured `TriageVerdict` using LangGraph and LLM reasoning.

[github.com/AnshSaxena05/cyberSecurity_alert_triage](https://github.com/AnshSaxena05/cyberSecurity_alert_triage)

## Problem

Agentic AI systems fail in production when they hand the LLM decisions that could be made deterministically. Choosing the query window, the source system, the time range and the API parameters are lookup tasks, not reasoning tasks. When an LLM makes them, results become unreliable and it can hallucinate.

## Design: determinism first

The system inverts that pattern. The LLM only reasons about meaning: what the data suggests, whether an alert is a false positive, and what the attacker's objective is. Plain Python code makes every structural decision.

The pipeline is split into two phases:

- **Phase 1: deterministic fast path** (target under 2 seconds). Raw alert JSON becomes typed entities, an ordered tool list and a tool budget. A small local model (`llama3.2:3b`) is used only for entity extraction in structured-output mode. If Ollama is unavailable, a heuristic regex fallback takes over.
- **Phase 2: LLM-driven deep path** (target under 60 seconds). A parallel tool burst fires before any LLM call, and a ReAct loop handles follow-up only. The output is a 6-section `TriageVerdict` as structured JSON.

## How it works

1. **Ingest.** `POST /ingest/{source}` accepts `splunk`, `crowdstrike`, `aws_guardduty`, `sentinel` or `generic`. `POST /ingest/auto` detects the source from the payload shape. Triage runs asynchronously: the POST returns `202` with an `alert_id`, and the client polls `GET /verdict/{alert_id}` until the status is `complete`.
2. **Normalise.** Every source maps to one OCSF-aligned `NormalizedAlert`. The `alert_id` is a SHA-256 hash of the source and its alert ID, so duplicate webhook deliveries get the same ID and can be deduplicated.
3. **Extract entities.** Pydantic enforces the `ExtractedEntities` schema (hosts, users, processes, techniques, IOCs), so malformed LLM output cannot reach the routing layer.
4. **Route by MITRE technique.** A technique-to-tools routing table gives O(1) lookup, falling back from a sub-technique to its parent technique and then to a default route. Look-back windows are fixed per signal type (for example 72h for process execution and 14d for authentication).
5. **Set a tool budget by severity.** The budget is 3 for LOW, 5 for MEDIUM, 8 for HIGH and 99 (effectively unlimited) for CRITICAL. The budget check runs in Python, so the LLM never controls it.
6. **Burst enrichment.** `asyncio.gather` fires all routed tools in parallel, each with its own timeout. A tool that fails degrades gracefully to `source_available: False` and never raises. Each result is compressed to at most 200 tokens before the LLM sees it.
7. **ReAct follow-up.** The LangGraph `agent_think` node can plan more tool calls. The loop continues only while the model asks for more tools and the budget is still positive.
8. **Generate verdict.** The verdict has 6 sections: severity with justification, MITRE assessments, a triage summary, confirmed IOCs (only those corroborated by enrichment), prioritised immediate actions, and an escalation decision (`ESCALATE_IR`, `FALSE_POSITIVE`, `MONITOR` or `CLOSE`) with rationale.

### Safety and reliability

- **Three guard layers:**
  - An input guard before normalisation: API-key check, a 1MB size limit and injection scrubbing.
  - A content filter before each tool call: it blocks dangerous SPL, blocks SSRF targets and bounds `hours_back`.
  - An output filter before logging or returning results.
- **Prompt-injection mitigation:** the LLM never writes SPL directly. A builder generates validated SPL from typed parameters, and even if the LLM is tricked into emitting raw SPL, the content filter blocks it.
- **IOC cache:** Redis with a 15-minute TTL and order-independent keys. The pipeline keeps working if Redis is unavailable.
- **Observability:** structured logging with structlog, optional LangSmith tracing, a per-alert cost tracker exposed at `GET /metrics`, and capture of analyst feedback when an analyst disagrees with a verdict.
- **Evaluation:** a golden set of 6 labelled alerts with expected severity and escalation. Scenarios include DNS tunnelling, ransomware pre-staging, AWS IAM privilege escalation and credential dumping.

## Stack

Python 3.14+ · FastAPI · LangGraph · Pydantic · Ollama or OpenAI (optional) · Redis (optional) · Langfuse (optional) · structlog · Docker Compose (app + Redis + Ollama) · pytest · uv.

Event-driven runtime (optional, behind a config flag): NATS JetStream for alert ingest, with a write-ahead log and a reconciler that republishes anything the broker missed · PostgreSQL via asyncpg (secrets and audit) · JWT service auth · OpenTelemetry with the OTLP exporter · ClickHouse in the dev stack · a Cloudflare Workers + Durable Objects gateway in TypeScript that holds analyst WebSockets at the edge · a Next.js frontend. The API is documented with OpenAPI, Swagger UI and ReDoc. The default test suite needs no external APIs.

## Links

- Source code: [github.com/AnshSaxena05/cyberSecurity_alert_triage](https://github.com/AnshSaxena05/cyberSecurity_alert_triage)
- Architecture doc: [docs/architecture.md](https://github.com/AnshSaxena05/cyberSecurity_alert_triage/blob/HEAD/docs/architecture.md)
- More about the author: [Ansh Saxena, Backend & ML Infrastructure Engineer at Cyware Labs, Bengaluru](https://anshsaxena05.github.io/)
- Design RFC: https://github.com/AnshSaxena05/cyberSecurity_alert_triage/blob/main/docs/rfcs/0001-deterministic-first-triage-and-durable-ingest.md
- Another open-source project: [JVM Concurrency Benchmarks](https://anshsaxena05.github.io/projects/jvm-concurrency-benchmarks.html.md)
