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
updated: 2026-06-02
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
| Component | Architectural unit of the system at function level — e.g. "Clickstream Ingestion Pipeline", not "MSK" (a tool) or "MSK variant" (a variant) |
| Clickstream | Stream of user interaction events captured from a frontend application (clicks, page views, chatbot sessions) |
| Tag Manager | A system (e.g. GTM) that manages and fires tracking tags/pixels on a website without requiring code deployments |
| CDP | Customer Data Platform — aggregates user behavioural data from multiple sources; can enrich and export events |
| Data Lake | Raw, unprocessed storage layer for events in their original form — typically S3-based, schema-on-read |
| Data Warehouse | Structured, query-optimised storage for aggregated metrics — typically Redshift or similar columnar store |
| Observability Stack | The collection of instrumentation, collection, storage, and visualisation components that make a system's behaviour inspectable |
| Instrumentation | Adding monitoring code (spans, metrics, logs) to an application so its behaviour can be observed at runtime |
| OTEL | OpenTelemetry — vendor-neutral, open-source framework for instrumentation, collection, and export of telemetry data |
| ADOT | AWS Distro for OpenTelemetry — AWS-managed and tested distribution of the OTEL collector; runs as a sidecar or standalone |
| Sidecar | A deployment pattern where a helper process (e.g. ADOT collector) runs alongside the main application container, handling cross-cutting concerns like telemetry export |
| ALB | Application Load Balancer — may front the clickstream ingestion endpoint, routing incoming events before they reach a producer (e.g. ECS service writing to MSK) |
| ECS | Elastic Container Service — may host custom clickstream event producers or ingestion microservices |
| MSK | Amazon Managed Streaming for Apache Kafka — managed Kafka cluster used as the event streaming backbone for clickstream ingestion |
| Firehose | Amazon Kinesis Data Firehose — managed delivery stream that buffers and delivers events to S3, Redshift, or other sinks |
| Lambda | AWS Lambda — serverless function; used for lightweight event transformation or routing between ingestion components |
| Redshift | AWS managed columnar data warehouse; stores aggregated clickstream and observability metrics for querying |
| Prometheus | Open-source metrics system; CloudWatch supports a Prometheus-compatible remote-write endpoint for metrics ingestion |
| Grafana | Open-source visualisation platform; can query CloudWatch, Prometheus, and Redshift to build unified dashboards |
| QuickSight | AWS-native BI and dashboard service; alternative to Grafana for visualising metrics within the AWS ecosystem |
