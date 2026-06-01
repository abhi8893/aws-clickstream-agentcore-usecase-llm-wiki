# Processed Sources Index

Catalog of all processed documents. Updated by `/wiki-process`.
See `schema.md` for entry format.

---

## Entries

<!-- /wiki-process appends entries below this line -->

## components

- **filepath**: `02-processed/components/`
- **filepath_raw**: `01-raw/brain-dump/components/components.md`
- **description**: First-pass component taxonomy defining the 12 architectural units of the system, distinct from AWS services or implementation variants
- **concepts**: [streaming-ingestion, otel-instrumentation, unified-dashboard]
- **components**: [web-application, agentcore-application, tag-manager, cdp-platform, clickstream-ingestion-pipeline, clickstream-processing-pipeline, clickstream-storage, data-warehouse, agent-otel-instrumentation-infra, agent-otel-backend, agent-observability, metrics-dashboard]
- **tags**: [kinesis-data-firehose, managed-streaming-kafka, lambda, glue, emr, cloudwatch, grafana, agentcore, opentelemetry, adot, x-ray, redshift]
- **status**: `draft`
- **wiki_pages**: `[]`

## usecase-context

- **filepath**: `02-processed/usecase-context/`
- **filepath_raw**: `01-raw/brain-dump/usecase-context/usecase_context.md`
- **description**: First-pass brain dump structuring the clickstream + AgentCore observability correlation problem across scenarios, requirements, and implementation options
- **concepts**: [streaming-ingestion, clickstream-sdk, server-side-tagging, agentcore-runtime, otel-instrumentation, unified-dashboard, event-correlation]
- **tags**: [kinesis-data-streams, kinesis-data-firehose, managed-streaming-kafka, google-tag-manager, aws-clickstream-sdk, opentelemetry, cloudwatch, agentcore, grafana, redshift, google-analytics, cdp, flink, emr, lambda]
- **status**: `draft`
- **wiki_pages**: `[]`
