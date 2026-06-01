# Defining Components


DISCLAIMER: This is not a fully accurate document as it's a brain dump. Please treat the information as incomplete, and potentially in-accurate. Do not use this document for creating hard facts.

The raw, processed and especially the finalized wiki documents / sections must have explicit component fields in addition to domain, tag, concepts etc

## What are components?

These are actual units of the architecture - which will be developed and refined iteratively. 
These are NOT the AWS services or software stack etc

For example, 

- MSK, Kinesis Data Streams, S3, Glue, Lambda, EC2 etc are not components. These are just tools/services we can use to build our components. 
- Clickstream Data Ingestion Pipeline, Clickstream Data Processing Pipeline are components. 
- Data Lake, Metrics Data Warehouse are components. 



For example, in our architecture, very very loosely and roughly, there can be following components:

- Main Web Application
  - Frontend
  - Backend

- Agentic Application
  - AWS AgentCore

- CDP / Marketing Analytics Platform

- Tag Manager (GTM)
    - Depends client or server side implementations

- Data Pipeline: Clickstream Ingestion
    - Might use MSK + Lambda + Firehose etc
    - May be after an ALB with an ECS deployment handling incoming requests and acting as producer to MSK -> data pipeline

- Data Pipeline: Clickstream processing
    - Might use Glue, EMR or MSK etc

- Agent Observability Components
    - SDK Instrumentation Infra (collector, ADOT sidecar etc)
    - Ingestion endpoint for observability data (CloudWatch)
        - OpenSearch (logs)
        - X-ray for traces
        - CloudWatch Metrics (Prometheus)
    - Visualization Layer 
        - CloudWatch 
        - OR Grafana + Langfuse + Prometheus

- Raw Clickstream Storage

- Data Lake Components

- Data Warehouse
    - Warehouse for final metrics

- Metrics Dashboard
    - Either AWS managed Grafana or Grafana Cloud or Quicksight etc
    

