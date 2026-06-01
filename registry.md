# Global Registry

Single source of truth for canonical terms across all three layers (01-raw, 02-processed, 99-wiki).

**Rules:**
- Always use canonical forms in `domain:`, `tags:`, `concepts:`, `components:` frontmatter fields
- LLM proposes new entries during `/wiki-ingest` or `/wiki-process` — human confirms before writing
- Run `/refine-terms` to detect duplicates and alias drift across the registry
- Run `/wiki-lint` to detect non-canonical terms in frontmatter across all files
- Add deprecated terms to the **Normalized** table so they are never reintroduced

---

## Field Definitions

| Field | Answers | Cardinality | Stability |
|---|---|---|---|
| `domain` | *"What topic space does this live in?"* | 1–3 per page | Stable — high-level categories |
| `tags` | *"What specific technologies/services are involved?"* | Many | Stable — tied to real tools |
| `concepts` | *"What generic, transferable idea or technique?"* | Few | Grows as wiki matures |
| `components` | *"Which part of OUR architecture does this relate to?"* | Few | Evolves with architecture |

---

## Domains

Coarse navigation — the top-level topic space a page lives in. Use 1–3 per page.

| Canonical |
|---|
| clickstream |
| agentcore |
| data-pipeline |
| dashboards |
| web-app |
| observability |

---

## Tags

Technology fingerprint — specific AWS services, tools, platforms. Many per page. Only non-obvious abbreviations listed as aliases.

### AWS Services
| Canonical | Aliases |
|---|---|
| kinesis-data-streams | kds |
| kinesis-data-firehose | kdf |
| managed-streaming-kafka | msk |
| lambda | — |
| s3 | — |
| redshift | — |
| cloudwatch | — |
| eventbridge | — |
| bedrock | — |
| api-gateway | — |
| cognito | — |
| athena | — |
| glue | — |
| emr | — |
| flink | — |
| adot | — |
| x-ray | — |

### Clickstream & Analytics
| Canonical | Aliases |
|---|---|
| google-tag-manager | gtm |
| google-analytics | ga4 |
| aws-clickstream-sdk | — |
| cdp | — |
| meta-pixel | — |
| adobe-analytics | — |
| hightouch | — |
| insider-cdp | — |

### Observability & Instrumentation
| Canonical | Aliases |
|---|---|
| opentelemetry | otel |
| cloudwatch-metrics | — |
| cloudwatch-logs | — |
| cloudwatch-traces | — |
| langfuse | — |
| grafana | — |
| arize | — |
| openllmetry | — |

---

## Concepts

Generic, transferable ideas and techniques — applicable beyond this specific system. Each should eventually have its own page in `99-wiki/concepts/`.

| Canonical | Brief definition |
|---|---|
| streaming-ingestion | Real-time event ingestion via a message broker |
| batch-ingestion | Scheduled bulk event ingestion |
| clickstream-sdk | Client-side SDK capturing user interaction events |
| server-side-tagging | Technique: GTM container running server-side — reduces client payload, improves data quality |
| client-side-tagging | Technique: tags fire directly in the user's browser — simpler setup, less server overhead |
| agentcore-runtime | AWS managed runtime for hosting agentic applications |
| otel-instrumentation | Adding OpenTelemetry SDK spans/metrics to application code |
| unified-dashboard | Single visualisation combining clickstream + agent metrics |
| event-correlation | Linking clickstream user events with agent observability metrics |
| medallion-architecture | Bronze/silver/gold layered data lake pattern |
| data-contract | Schema agreement between producer and consumer of a data stream |
| llm-as-judge | Using an LLM to evaluate the quality of another LLM's responses |
| rag-retrieval | Retrieval-Augmented Generation — fetching context docs at inference time |

---

## Components

Architectural units of THIS specific system — function-level, not service-level, not variant-level. The `parent` column captures hierarchy; flat slugs are used in frontmatter.

| Canonical | Description | Parent |
|---|---|---|
| web-application | User-facing website (frontend + backend) | — |
| agentcore-application | Agentic LLM app deployed on AWS AgentCore | — |
| tag-manager | GTM instance (client or server-side) collecting events | — |
| cdp-platform | CDP or marketing analytics platform (Insider, Amplitude, etc.) | — |
| clickstream-ingestion-pipeline | Pipeline ingesting raw clickstream events to AWS | — |
| clickstream-processing-pipeline | Pipeline aggregating raw events into metrics | — |
| clickstream-storage | Raw event and processed data storage (S3 / data lake) | — |
| data-warehouse | Aggregated metrics store (Redshift, etc.) | — |
| agent-otel-instrumentation-infra | OTEL instrumentation layer for AgentCore (collector, ADOT sidecar, SDK) | agent-observability |
| agent-otel-backend | OTEL data ingestion backend (CloudWatch logs/metrics/traces, Prometheus) | agent-observability |
| agent-observability | Full observability stack for AgentCore (instrumentation → backend → viz) | — |
| metrics-dashboard | Unified visualisation surface (Grafana, CloudWatch, QuickSight) | — |

*Note: Components are loosely defined at this stage and will be refined iteratively as the architecture matures.*

---

## Deprecated / Normalized

Terms that have been superseded — never use these in frontmatter.

| Old term | Replaced by | Date |
|---|---|---|
| agentcore (as tag) | agentcore (domain) | 2026-06-01 |
| server-side-tagging (as tag) | server-side-tagging (concept) | 2026-06-01 |
| client-side-tagging (as tag) | client-side-tagging (concept) | 2026-06-01 |
