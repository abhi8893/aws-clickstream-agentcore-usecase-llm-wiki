---
type: processed-section
title: "Requirements (R1–R6)"
parent: "[[overview]]"
domain: [clickstream, agentcore, dashboards, observability]
tags: [cloudwatch, grafana, opentelemetry, agentcore, google-tag-manager, aws-clickstream-sdk]
concepts: [unified-dashboard, event-correlation, otel-instrumentation]
status: draft
created: 2026-05-31
updated: 2026-05-31
---

## Summary

Six requirements defining what the solution must deliver. R1-R3 are data collection requirements per metric type. R4 defines the dashboard output. R5 and R6 are cross-cutting requirements that generate architecture variants — every pipeline decision should be evaluated against both options in R5 and R6.

## Content

### R1: General Website Clickstream Events

Collect, store, analyse, and visualise general website engagement events to understand user behaviour on the site.

*Feeds into:* R4 (dashboard), R6 (correlation granularity)

---

### R2: Chatbot-Specific Clickstream Events

Collect, store, analyse, and visualise chatbot-specific interaction events (e.g. `chatbot_icon_clicked`, `chatbot_session_start`, `chatbot_session_end`) to understand chatbot adoption.

*Must be correlated with:* R1 (general website events) — a user's chatbot interaction is part of their broader website session.

---

### R3: Agent Performance Observability

Collect AgentCore observability events and define custom agent performance metrics:

**Example custom metrics:**
- Number of guardrail hits per session
- Number of tool calls per request
- Knowledge source fetch failure rate
- Average planning turns per request
- Model inference latency (p95)

**Implementation path:** Custom OTEL instrumentation in agent application → CloudWatch → metric aggregation → dashboard

*Must be correlated with:* R1 and R2 — agent performance metrics should be joinable with clickstream engagement data.

---

### R4: Chatbot Adoption Dashboard

Answer questions like: *"Chatbot adoption metrics were low today because the agent was unable to fetch knowledge documents and kept returning default responses."* (Low chatbot session duration events + High knowledge fetch failure rate)

**Variants:**
- `R4.1` — Separate dashboards: one for clickstream (R1+R2), one for agent observability (R3)
- `R4.2` — Single unified dashboard combining R1 + R2 + R3

Both variants should be documented. R4.2 is the more valuable but harder to implement.

---

### R5: Adoption Metrics Refresh Frequency

Drives the ingestion and processing pipeline architecture choice.

**Variants:**
- `R5.1` — Batch: metrics refreshed on a schedule (hourly, daily). Simpler pipeline (EMR, Glue).
- `R5.2` — Real-time: metrics update as events arrive. More complex pipeline (Flink, Kinesis).

**Architecture variants must be documented for both.** The choice significantly affects cost, complexity, and latency.

---

### R6: Correlation Granularity

Determines how deeply user website interactions can be joined with agent performance data.

**Variants:**
- `R6.1` — **Aggregate level**: daily or hourly summaries are correlated. Per-user session analysis not possible. Lower complexity, sufficient for most adoption questions.
  - *Example:* "Adoption was low on Tuesday because guardrail hit rate was high"
- `R6.2` — **Session level**: per-user, per-session deepdives are possible. Agent traces can be linked to specific user interaction sequences.
  - *Note:* Source doc suggests this level may never be needed in practice — isolated session deepdives are already possible via CloudWatch traces.

**Recommendation from source:** R6.1 is likely sufficient. R6.2 adds significant complexity for marginal value.

## Glossary

| Term | Definition |
|------|------------|
| Guardrail hit | AgentCore safety mechanism intercepting a response — metric indicating agent constraints were triggered |
| Knowledge fetch failure | Agent failed to retrieve a document from its knowledge base |
| Adoption metric | Aggregate measure of how actively users engage with the chatbot |
| Session duration | Time from chatbot session start to end event |

## Related Sections

- [[section-1-scenarios]] — scenario choices (S4, S6) directly affect how R1/R2 are collected
- [[section-3-implementation]] — each requirement maps to one or more implementation options
