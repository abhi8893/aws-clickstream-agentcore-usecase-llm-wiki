# Purpose

## What this wiki is

A research knowledge base and architecture reference for designing a full-stack solution that:
1. Tracks user engagement via clickstream events on a website hosting an AWS AgentCore chatbot
2. Captures agent performance observability metrics from AgentCore via OpenTelemetry → CloudWatch
3. Correlates both in a unified dashboard to answer: *"Why did chatbot adoption drop today?"*

## Goals

- Produce **reusable architecture guidance** for "clickstream ingestion + agent observability + unified dashboards" on AWS
- Document **multiple scenario variants** (GTM vs SDK, batch vs streaming, CMS vs custom web app) with tradeoffs
- Build a **concept glossary** covering AWS services, data engineering, clickstream, GTM, and observability
- Generate **how-to guides** and **data pipeline design docs** suitable for sharing on GitHub

## Key Questions

1. What are the best architecture patterns for ingesting clickstream events at scale on AWS?
2. How do you instrument an AWS AgentCore application with custom OTEL metrics?
3. What is the right pipeline for correlating clickstream engagement metrics with agent performance metrics?
4. How does the architecture change for batch vs. streaming refresh frequency?
5. What is the minimum viable unified dashboard linking user engagement and agent observability?

## Scope

**In scope:**
- Clickstream event collection (GTM client-side, GTM server-side, AWS Clickstream SDK, third-party CDPs)
- Clickstream ingestion pipelines on AWS (MSK, Kinesis, Firehose, Lambda)
- AWS AgentCore deployment patterns and custom OTEL instrumentation
- CloudWatch metrics, logs, traces for agent observability
- Unified dashboards (Grafana, CloudWatch Dashboards)
- Architecture variants for CMS websites (WordPress/Shopify) and custom web apps
- Concept glossary: AWS services, data engineering, clickstream, GTM, observability

**Out of scope:**
- Deep implementation code (indicative snippets only)
- Infrastructure-as-code (Terraform/CDK) specifics
- Non-AWS agent frameworks (LangChain, CrewAI standalone)
- User authentication and identity management details

## Evolving Thesis

> First pass: A unified architecture pairing GTM server-side tagging → Firehose → S3/Redshift for clickstream with AgentCore OTEL auto-instrumentation → CloudWatch Metrics for agent performance, visualized together in Grafana, provides the best balance of operational simplicity and correlation granularity.

*This thesis should be updated as sources are ingested and architecture variants are evaluated.*

## Current Status

> Wiki initialised 2026-05-31. No sources ingested yet. Raw layer being populated.
