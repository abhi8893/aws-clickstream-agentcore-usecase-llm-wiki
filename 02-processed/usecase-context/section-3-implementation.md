---
type: processed-section
title: "Implementation Options (I1–I8)"
parent: "[[overview]]"
domain: [clickstream, agentcore, data-pipeline, observability]
tags: [kinesis-data-streams, kinesis-data-firehose, managed-streaming-kafka, lambda, flink, emr, opentelemetry, cloudwatch, agentcore, redshift, google-tag-manager, aws-clickstream-sdk]
concepts: [streaming-ingestion, otel-instrumentation, agentcore-runtime]
status: draft
created: 2026-05-31
updated: 2026-05-31
---

## Summary

Eight implementation areas covering the full pipeline from frontend event capture through to observability backends. I1-I3 cover the frontend/collection layer. I4-I5 cover the AWS ingestion and processing pipeline. I6-I8 cover AgentCore deployment and instrumentation. Many options are cross-combinations of scenario and requirement choices — they should be treated as independent sub-problems, not a single fixed architecture.

## Content

### I1: Chatbot Interaction Clickstream Events

How chatbot-specific events are captured at the frontend.

**Options:**
- `I1.1` — **Google Tag Manager**: Custom GTM tags fire on chatbot interaction events (e.g. button clicks, session start/end). Works for both CMS and custom web apps.
- `I1.2` — **Custom SDKs**: Instrument frontend code directly with SDKs (AWS Clickstream SDK, Hightouch, etc.) to emit events like `chatbot_icon_clicked`, `chatbot_session_start`.

---

### I2: Chatbot Adoption Metrics Aggregation

Where adoption metrics are computed from raw events.

**Options:**
- `I2.1` — **CDP / analytics platform**: Metrics defined and aggregated inside the CDP or analytics tool (e.g. Insider, Amplitude). Lower engineering effort; dependent on platform capabilities.
- `I2.2` — **Custom aggregation**: Raw events collected and aggregated via a custom pipeline on AWS. More flexible; required if no CDP is used or CDP doesn't support the needed metrics.

---

### I3: Chatbot Engagement Event Export to AWS

*Applies when `I2.2` (custom aggregation) or when CDP data needs to flow to AWS.*

**Options:**
- `I3.1` — **CDP / analytics platform export**: e.g. Insider CDP → S3 → Redshift. Relies on platform's native export capability. Batch-oriented.
- `I3.2` — **Clickstream SDKs direct to AWS**: e.g. AWS Clickstream SDK → MSK → S3 → Redshift. Real-time capable; no CDP intermediary.

---

### I4: Clickstream Ingestion Pipeline

*Data pipeline design area.* Architecture for ingesting and routing clickstream events to AWS storage.

**Example patterns (to be expanded into architecture docs):**
- GTM server-side → HTTP endpoint → Kinesis Data Firehose → S3
- AWS Clickstream SDK → MSK (Kafka) → S3 / Redshift
- Kinesis Data Streams → Lambda (transform) → Firehose → S3
- Direct Firehose → S3 with Lambda buffering

*Cross-reference:* R5 (batch vs real-time) determines which patterns are applicable.

---

### I5: Clickstream Processing

*Data pipeline design area.* Transforming raw clickstream events into aggregated metrics.

**Streaming options (R5.2):**
- Apache Flink (Amazon Managed Service for Apache Flink) → Redshift / S3
- Kinesis Data Streams with Lambda for lightweight transformations

**Batch options (R5.1):**
- Amazon EMR (Spark) → Redshift
- AWS Glue → S3 / Redshift

---

### I6: AgentCore Deployment

Baseline AgentCore architecture required as context for instrumentation design.

- Template agentic application deployed on AgentCore runtime
- Key components: planner agent, tool definitions (Lambda), knowledge base, memory, gateway
- Instrumentation decisions depend on the specific agent architecture

*Note from source:* Only basic context needed here — detailed AgentCore architecture is a separate documentation area.

---

### I7: AgentCore Custom Instrumentation

How agent performance metrics are captured. Source identifies three key dimensions:

**Dimension 1: Dev vs Prod**
- Development: evaluation against labelled ground truth
- Production: monitoring with "indicators" + optional LLM-as-judge async evaluation

**Dimension 2: Metric categories**
- Agent-specific (guardrail hits, default response rate)
- Reasoning/planning (turns per request, thinking token consumption)
- Tool usage (call count, error rate per tool)
- Memory & knowledge retrieval (fetch failures, cache hit rate)

**Dimension 3: Instrumentation approach**
- `Option A` — Compute metrics from **existing auto-instrumented traces**: configure OTEL auto-instrumentation on AgentCore → extract attributes (e.g. `gen_ai.usage.input_tokens`, span duration) → aggregate in CloudWatch
- `Option B` — **Manual OTEL instrumentation**: add explicit spans, metrics, or baggage using OTEL SDK or OpenLLMetry. Required for complex metrics (e.g. loop detection, RAG retrieval chunk relevancy).

**Infra vs application distinction:**
- Infra-level: Lambda function latency, API Gateway timeouts
- Application-level: planner churning thinking tokens, wrong tool call loops

---

### I8: AgentCore Instrumentation Backends

Where OTEL data flows after leaving the AgentCore application.

**AWS-native path:**
- AgentCore → OTEL → CloudWatch (logs + metrics + X-Ray traces)
- CloudWatch under the hood: OpenSearch (logs), X-Ray (traces), managed Prometheus-compatible service (metrics)

**Third-party backends (optional):**
- Langfuse — open-source LLM observability
- Arize — ML/LLM observability platform
- Databricks AgentBricks — managed evaluation platform

**Open question from source:** What other AWS services does CloudWatch use under the hood for storage? (OpenSearch for logs, X-Ray for traces confirmed — metrics backend unclear.)

## Glossary

| Term | Definition |
|------|------------|
| OpenLLMetry | Specialized OpenTelemetry instrumentation library for LLM applications |
| LLM-as-judge | Using an LLM to evaluate the quality of another LLM's responses |
| Baggage | OTEL mechanism for propagating key-value metadata across service boundaries |
| Chunk relevancy | RAG metric measuring how relevant retrieved document chunks are to the query |
| Loop detection | Identifying when an agent repeatedly calls the wrong tool or re-plans unnecessarily |
| AgentBricks | Databricks managed platform for LLM agent evaluation and monitoring |
| Arize | LLM/ML observability platform supporting OTEL ingestion |

## Related Sections

- [[section-1-scenarios]] — S4 (collection method) directly determines I1/I3 options
- [[section-2-requirements]] — R5 (batch/streaming) determines I4/I5; R3 (agent observability) maps to I7/I8
