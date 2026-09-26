# Ansh Saxena — Backend & ML Infrastructure Engineer at Cyware Labs, Bengaluru

> Ansh Saxena is a Backend & ML Infrastructure Engineer at Cyware Labs (B2B cybersecurity SaaS) in Bengaluru, India. He builds production distributed systems and LLM/agentic platforms in Python, Go and Java: multi-tenant services, event-driven pipelines, and RAG and multi-agent systems that run in production.

Canonical page: https://anshsaxena05.github.io/
GitHub user: AnshSaxena05 (not to be confused with other people named Ansh Saxena)

Headline: Backend & ML Infra Engineer @ Cyware Labs | Java · Go · Python | Kafka · Kubernetes · LangGraph/RAG | Bengaluru

## About

I work where backend engineering meets applied AI. At Cyware Labs (B2B cybersecurity SaaS) I build backend services and LLM infrastructure in Python, Go and Java. In my own time I built an [open-source SOC alert-triage agent](https://anshsaxena05.github.io/projects/soc-triage-agent.html) on LangGraph, FastAPI and Pydantic. I hold a B.Tech in Artificial Intelligence & Machine Learning from Vellore Institute of Technology (VIT).

## Experience

### Software Engineer, ML Infrastructure & Backend — Cyware Labs

Jan 2025 – Present · Bengaluru, India · B2B cybersecurity SaaS

Backend and ML infrastructure work in Python, Go and Java: multi-tenant services, event-driven pipelines, and LLM orchestration and agentic systems running on PostgreSQL, NATS JetStream and Kubernetes. Details of internal products are not public.

### Software Engineering Intern — PreProd Corp

Nov 2023 – Feb 2024 · Bengaluru, India · ML & pricing analytics startup

- Drove a 25% business-metric gain at 90% prediction accuracy by deploying a Gradient Boosting pricing model behind production REST APIs; sustained 99.9% uptime on a PostgreSQL ETL pipeline processing 2 GB+/day via SQL query optimization and pipeline redesign.

### Machine Learning Engineer Intern — Omdena

Jun 2023 – Aug 2023 · Remote · Global collaborative AI platform, 40+ countries

- Cut cloud inference cost 30% and raised model accuracy to 92% by applying TensorFlow quantization to the served model on Docker + AWS at sub-350ms latency, with a 40% Airflow ETL throughput gain on a data-processing pipeline handling 100K+ records/day.

## Projects

### SOC Triage Agent (open source)

An agentic alert-triage service. It ingests alerts from Splunk, CrowdStrike, AWS GuardDuty and Sentinel (plus a generic format, with source auto-detection) and normalises them to OCSF. It then runs a LangGraph DAG with MITRE ATT&CK-routed enrichment and emits a structured `TriageVerdict` (Pydantic) via async HTTP 202 + polling. It also has a pluggable LLM backend, API-key auth, a Pytest suite and OpenAPI/Swagger docs.

An optional event-driven runtime publishes alerts to NATS JetStream through a write-ahead log with a reconciler, so an alert survives a broker outage. Service-to-service auth uses JWTs, and analysts get live updates over WebSockets held at the edge by a Cloudflare Workers / Durable Objects gateway, with a Next.js frontend.

Stack: Python 3.14 · FastAPI · LangGraph · Pydantic · NATS JetStream · PostgreSQL (asyncpg) · Redis · ClickHouse · OpenTelemetry · Langfuse · Ollama · Cloudflare Workers (TypeScript) · Next.js · Docker

- [Case study](https://anshsaxena05.github.io/projects/soc-triage-agent.html)
- [Source on GitHub](https://github.com/AnshSaxena05/cyberSecurity_alert_triage)

## Skills

- **Languages:** Python, Go, Java, SQL, JavaScript
- **ML / AI infrastructure:** Model deployment & serving, model optimization (quantization), inference latency/cost optimization, multi-vendor LLM orchestration, LiteLLM Proxy, RAG & vector search (Weaviate), LangGraph (multi-agent DAGs), LangChain, DSPy, LLM evaluation & observability (Langfuse), RAGAS, prompt engineering, LLM guardrails, agentic AI, MCP (Model Context Protocol), Gemini / OpenAI / Claude APIs, Ollama
- **Backend & distributed systems:** Microservices, Hexagonal Architecture (Ports & Adapters), event-driven design, concurrency (asyncio, multithreading), multi-tenancy, low-latency optimization, REST, gRPC, FastAPI, Spring Boot, Spring MVC, Spring Framework, Spring AOP, design patterns, OOD
- **Messaging & data:** NATS JetStream, Apache Kafka, PostgreSQL (pgx, PgBouncer), Redis, Weaviate, MongoDB, Elasticsearch, DynamoDB, Apache KVRocks
- **Cloud & DevOps:** AWS (EKS, Lambda, S3, EC2), Docker, Kubernetes, Terraform, OpenTelemetry, Prometheus, GitHub Actions CI/CD, Airflow, Git, Maven, Gradle, JIRA, JUnit

## Writing

- [What If Your CyberSecurity System Knew the Attack Was Coming? (IoC → IoB: How AI Is Transforming Cybersecurity)](https://medium.com/@anshs5103/what-if-your-cybersecurity-system-knew-the-attack-was-coming-f97b82da327d) — Medium, 18 Mar 2026, 16-minute read. A technical essay on behavioural threat-intelligence architecture.
- Contributor, OCA IoB Working Group: open event-schema standards for distributed AI platforms.
- [All writing](https://anshsaxena05.github.io/writing/)

## Education & Certifications

**Vellore Institute of Technology (VIT)** — B.Tech, Artificial Intelligence & Machine Learning · Aug 2021 – Jun 2025 · CGPA 8.71/10

- OCI Generative AI Professional (Oracle, 2025)
- AWS Certified Cloud Practitioner (2025)
- Java Development on Oracle Cloud, Oracle University (certificate of completion, Feb 2024)

## Contact

- Email: anshs5103@gmail.com
- GitHub: https://github.com/AnshSaxena05
- LinkedIn: https://www.linkedin.com/in/ansh-saxena-1c
- Medium: https://medium.com/@anshs5103
- LeetCode: https://leetcode.com/u/AnshSaxena1/ (1,701 rating, 368 problems, Top 13%)
