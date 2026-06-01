---
type: processed-section
title: "Deployment & Collection Scenarios (S1–S6)"
parent: "[[overview]]"
domain: [clickstream, web-app]
tags: [google-tag-manager, aws-clickstream-sdk, google-analytics, cdp]
concepts: [clickstream-sdk, server-side-tagging, client-side-tagging]
status: draft
created: 2026-05-31
updated: 2026-05-31
---

## Summary

Six scenario axes that determine the overall architecture shape. Each axis has 2+ options; most are open (all combinations valid) except where `FIX SCENARIO` is noted. These axes are independent — a choice in S1 affects S4 options but does not force S6.

## Content

### S1: User-Facing Web Application Type

**Options:**
- `S1.1` — CMS platform (WordPress, Shopify, etc.)
- `S1.2` — Custom-built web application stack

**Why it matters:**
- GTM installation differs: CMS platforms may have native plugins; custom apps require manual header injection
- Chatbot integration differs: CMS = custom JS plugin; custom app = bespoke integration
- `S1.1` is marked **FIX SCENARIO** — documentation should cover both S1.1 and S1.2

---

### S2: Custom Web Application Deployment

*Applies only when `S1.2` (custom web app)*

**Options:**
- `S2.1` — Deployed on AWS
- `S2.2` — Deployed on non-AWS infrastructure

**Note:** Full-stack web application details are lower priority. Focus should be on: (a) clickstream ingestion → aggregation → visualisation, (b) AgentCore chatbot integration, (c) agent telemetry → CloudWatch → visualisation.

---

### S3: Chatbot Integration in Web Application

**Options:**
- `S3.1` — Chatbot integrated as part of the main website (same domain/session)
- `S3.2` — Standalone application with dedicated domain

**Fixed:** `S3.1` — chatbot is embedded in the website. This simplifies clickstream event correlation since chatbot events and website events share the same session context.

---

### S4: Clickstream Event Collection Method

**Options:**
- `S4.1` — Google Tag Manager (client-side or server-side)
- `S4.2` — Custom SDKs (AWS Clickstream SDK, Hightouch, Adobe, Google Analytics, etc.)

**Note:** Both options should be documented as architecture variants. They determine where events are captured and how they flow to AWS.

---

### S5: User Behaviour Analysis Platforms

**Options:**
- `S5.1` — Google Analytics (GA4)
- `S5.2` — Meta Pixel
- `S5.3` — Google Ads
- `S5.4` — CDPs: Insider, Salesforce, Amplitude

**Why it matters:**
- These platforms hold both granular events and aggregated reports
- GA4 natively streams only to BigQuery — may require additional pipeline to AWS
- Insider CDP can export granular events to AWS: S3 → Redshift
- User behaviour data needs to be correlated with chatbot adoption + agent performance

---

### S6: GTM Deployment Side

**Options:**
- `S6.1` — Client-side GTM (tag fires in user browser)
- `S6.2` — Server-side GTM (GTM container runs on a server, reduces client payload)

**Both should be considered.** Server-side GTM changes the data flow significantly — events go to the GTM server container first, then forwarded to destinations. This affects latency, data quality, and PII handling.

## Glossary

| Term | Definition |
|------|------------|
| CMS | Content Management System (WordPress, Shopify, etc.) |
| Client-side GTM | GTM where tags fire in the user's browser — direct to analytics destinations |
| Server-side GTM | GTM container hosted server-side — browser sends to GTM server, which forwards to destinations |
| Meta Pixel | Facebook/Meta analytics tracking script |
| Hightouch | Reverse ETL / CDP tool for syncing data warehouse data to marketing tools |

## Related Sections

- [[section-2-requirements]] — requirements reference these scenario axes (e.g. R4 references S4)
- [[section-3-implementation]] — implementation options are scoped per scenario choice
