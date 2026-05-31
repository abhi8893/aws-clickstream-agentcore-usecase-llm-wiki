# Global Registry

Single source of truth for canonical terms across all three layers (01-raw, 02-processed, 99-wiki).

**Rules:**
- Always use canonical forms in `domain:`, `tags:`, `concepts:` frontmatter fields
- LLM proposes new entries during `/wiki-ingest` — human confirms before writing
- Run `/wiki-lint` to detect non-canonical terms across all files
- Add deprecated terms to the **Normalized** table so they are never reintroduced

---

## Domains

Canonical domain names for `domain:` frontmatter field.

| Canonical | Aliases (normalize → canonical) |
|---|---|
| clickstream | click-stream, clickstream-analytics, clickstream-events |
| agentcore | aws-agentcore, agent-core, bedrock-agentcore, agentcore-runtime |
| data-pipeline | de-pipeline, data-pipelines, pipeline |
| dashboards | visualization, reporting, observability-dashboards |
| web-app | frontend, web-application, website |
| observability | monitoring, telemetry, otel |

---

## Tags

Canonical tags for `tags:` frontmatter field.

### AWS Services
| Canonical | Aliases |
|---|---|
| kinesis-data-streams | kinesis, kds, kinesis-streams |
| kinesis-data-firehose | firehose, kdf, kinesis-firehose |
| managed-streaming-kafka | msk, kafka, amazon-msk |
| lambda | aws-lambda |
| s3 | amazon-s3 |
| redshift | amazon-redshift |
| cloudwatch | amazon-cloudwatch, cw |
| eventbridge | amazon-eventbridge, event-bridge |
| bedrock | amazon-bedrock |
| agentcore | aws-agentcore, bedrock-agentcore |
| api-gateway | amazon-api-gateway |
| cognito | amazon-cognito |
| athena | amazon-athena |
| glue | aws-glue |
| emr | amazon-emr |
| flink | amazon-managed-flink, apache-flink |

### Clickstream & Analytics
| Canonical | Aliases |
|---|---|
| google-tag-manager | gtm, tag-manager |
| google-analytics | ga4, google-analytics-4 |
| aws-clickstream-sdk | clickstream-sdk, aws-sdk-clickstream |
| server-side-tagging | sst, server-side-gtm |
| client-side-tagging | client-side-gtm |
| cdp | customer-data-platform |

### Observability & Instrumentation
| Canonical | Aliases |
|---|---|
| opentelemetry | otel, open-telemetry |
| x-ray | aws-x-ray, xray |
| cloudwatch-metrics | cw-metrics, custom-metrics |
| cloudwatch-logs | cw-logs |
| cloudwatch-traces | cw-traces |
| langfuse | |
| grafana | |

### Architecture Patterns
| Canonical | Aliases |
|---|---|
| streaming-ingestion | real-time-ingestion, stream-ingestion |
| batch-ingestion | batch-processing |
| event-schema | event-structure, event-format |
| medallion-architecture | bronze-silver-gold, lakehouse |
| data-contract | schema-contract |

---

## Concepts

Key concepts with brief definitions and wiki links (added as wiki pages are created).

| Canonical | Brief definition | Wiki link |
|---|---|---|
| streaming-ingestion | Real-time event ingestion via a message broker | — |
| clickstream-sdk | Client-side SDK capturing user interaction events | — |
| server-side-tagging | GTM container running server-side to reduce client payload | — |
| agentcore-runtime | AWS managed runtime for hosting agentic applications | — |
| otel-instrumentation | Adding OpenTelemetry SDK spans/metrics to application code | — |
| unified-dashboard | Single visualization combining clickstream + agent metrics | — |
| event-correlation | Linking clickstream user events with agent observability metrics | — |

---

## Deprecated / Normalized

Terms that have been superseded — never use these in frontmatter.

| Old term | Replaced by | Date |
|---|---|---|
| _empty_ | — | — |
