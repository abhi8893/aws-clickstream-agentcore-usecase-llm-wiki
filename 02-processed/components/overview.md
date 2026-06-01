---
type: processed-overview
title: "System Components — Architectural Units"
source_file: "../../01-raw/brain-dump/components/components.md"
source_type: brain-dump
domain: [clickstream, agentcore, data-pipeline, observability, dashboards, web-app]
tags: [kinesis-data-firehose, managed-streaming-kafka, lambda, glue, emr, cloudwatch, grafana, agentcore, opentelemetry, adot, x-ray, redshift]
concepts: [streaming-ingestion, otel-instrumentation, unified-dashboard]
components: [web-application, agentcore-application, tag-manager, cdp-platform, clickstream-ingestion-pipeline, clickstream-processing-pipeline, clickstream-storage, data-warehouse, agent-otel-instrumentation-infra, agent-otel-backend, agent-observability, metrics-dashboard]
status: draft
created: 2026-06-01
updated: 2026-06-01
---

## Summary

A brain dump establishing the first-pass component taxonomy for the system — the architectural units that make up the solution, distinct from the AWS services used to implement them. Components are function-level (e.g. "Clickstream Ingestion Pipeline"), not service-level (e.g. "MSK") or variant-level. This document is explicitly incomplete and inaccurate — it is scaffolding to seed the registry, not a final architecture. Components will be refined iteratively as more knowledge documents are ingested.

## Key Concepts

- **Components vs tags** — Components are architectural units of THIS system ("what part of our system?"). Tags are specific technologies ("what tools?"). A `clickstream-ingestion-pipeline` component may use `managed-streaming-kafka` + `lambda` + `kinesis-data-firehose` tags — all different levels of description.
- **Function-level, not variant-level** — `clickstream-ingestion-pipeline` covers all implementation variants. Which services are used goes in `tags`; which variant was chosen goes in a `decision-record`.
- **Components evolve** — This first-pass list is deliberately loose. Components will be split, merged, renamed as architecture matures.

## Component Map

| Component | Description | Parent |
|---|---|---|
| `web-application` | User-facing website (frontend + backend) | — |
| `agentcore-application` | Agentic LLM app deployed on AWS AgentCore | — |
| `tag-manager` | GTM instance (client or server-side) collecting events | — |
| `cdp-platform` | CDP or marketing analytics platform (Insider, Amplitude, etc.) | — |
| `clickstream-ingestion-pipeline` | Pipeline ingesting raw clickstream events to AWS | — |
| `clickstream-processing-pipeline` | Pipeline aggregating raw events into metrics | — |
| `clickstream-storage` | Raw event and processed data storage (S3 / data lake) | — |
| `data-warehouse` | Aggregated metrics store (Redshift, etc.) | — |
| `agent-otel-instrumentation-infra` | OTEL instrumentation layer (collector, ADOT sidecar, SDK) | `agent-observability` |
| `agent-otel-backend` | OTEL data backend (CloudWatch logs/metrics/traces, Prometheus) | `agent-observability` |
| `agent-observability` | Full observability stack for AgentCore (instrumentation → backend → viz) | — |
| `metrics-dashboard` | Unified visualisation surface (Grafana, CloudWatch, QuickSight) | — |

## Glossary

| Term | Definition |
|------|------------|
| Component | Architectural unit of the system — function-level, not service or variant level |
| ADOT | AWS Distro for OpenTelemetry — AWS-managed OTEL collector distribution |
| ALB | Application Load Balancer — potential ingestion entry point before MSK |
| ECS | Elastic Container Service — may host custom ingestion producers |
