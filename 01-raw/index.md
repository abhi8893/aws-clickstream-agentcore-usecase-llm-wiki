# Raw Sources Index

Catalog of all source documents. Updated by `/wiki-ingest`.
See `schema.md` for entry format. See `registry.md` for canonical tags and domains.

---

## Entries

<!-- /wiki-ingest appends entries below this line -->

## [2026-06-01] components

- **filepath**: `01-raw/brain-dump/components/components.md`
- **filetype**: `markdown`
- **status**: `processed`
- **domain**: `[clickstream, agentcore, data-pipeline, observability, dashboards, web-app]`
- **tags**: `[kinesis-data-firehose, managed-streaming-kafka, lambda, glue, emr, cloudwatch, grafana, agentcore, opentelemetry, adot, x-ray, redshift]`
- **components**: `[web-application, agentcore-application, tag-manager, cdp-platform, clickstream-ingestion-pipeline, clickstream-processing-pipeline, clickstream-storage, data-warehouse, agent-otel-instrumentation-infra, agent-otel-backend, agent-observability, metrics-dashboard]`
- **notes**: Brain dump defining architectural components of the system. Self-described as incomplete and potentially inaccurate — treat as scaffolding. Establishes the component taxonomy; components will be refined iteratively.
- **processed_at**: `02-processed/components/`
- **metadata**: Brain dump markdown, ~60 lines, authored by project owner

## [2026-05-31] usecase-context

- **filepath**: `01-raw/brain-dump/usecase-context/usecase_context.md`
- **filetype**: `markdown`
- **status**: `processed`
- **domain**: `[clickstream, agentcore, dashboards, web-app, data-pipeline, observability]`
- **tags**: `[kinesis-data-streams, kinesis-data-firehose, managed-streaming-kafka, google-tag-manager, aws-clickstream-sdk, opentelemetry, cloudwatch, agentcore, grafana, redshift]`
- **notes**: First-pass problem statement covering scenarios (S1-S6), requirements (R1-R6), and implementation options (I1-I8). Self-described as incomplete and potentially incorrect — treat as scaffolding, not ground truth.
- **processed_at**: `02-processed/usecase-context/`
- **metadata**: Brain dump markdown, ~270 lines, authored by project owner
