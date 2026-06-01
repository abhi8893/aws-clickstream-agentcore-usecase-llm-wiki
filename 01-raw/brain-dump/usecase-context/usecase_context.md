# Usecase Context

## Problem Statement

A chatbot is deployed in a user facing application (exposed through a frontend website). The chatbot is part of agentic application deployed on AWS AgentCore. The goal is to track adoption metrics of the chatbot + solution on the website and correlate them with agentic observability performance.

This may involve capturing, processing, and aggregating clickstream data from the frontend to convert into engagement metrics. AWS AgentCore captures the observability metrics (otel) from the agentic application, and routes them to Cloudwatch (which handles logs, traces, metrics) + provides visualizations.

Since clickstream data is not visualized in cloudwatch, and agent observabulity metrics appear in cloudwatch - A unified dashboard needs to be created to visualize engagement metrics and correlate agent observability metrics.

For example, we see a low user engagament metric / adoption rate on days where the agent chatbot took too long to respond, or hit the guardrails too many times, or defaulted to template response ("I am sorry I can't help you with that").

The engagement / adoption metrics can come from clickstream events tracked in the frontend code (for e.g. using aws clickstream sdk), and the agent observability metrics can be tracked using custom instrumentation and creating custom metrics in cloudwatch. The 2 metrics need to be correlated.

The following sections divide the context into Scenario, Requirements, Solutioning, and Implementation details.

What do the following sections contain?

  - This is my own 1st pass at structuring the problem statement. It is NOT comprehensive. 
  - Lists the various scenarios, requirements, and implementation details that will be needed to be taken into consideration while developing the architecture.
  - Any sub-scenario / sub-requirement / sub-implementation choices can be valud, and that will affect how any requirements is fulfilled / implementation may happen. 
  - Add more scenarios, requirements, implementation details based on your understanding of the documents + web search.
  - There can be resultant, many cross combination of possible scenarios and solutions. 

What it is not?

  - It is NOT a comprehensive research. Take this as the initial guiding doc for structuring the wiki.
  - There can be INCORRECT details. Please do your own research based on the documents provided + web search.
  - DO NOT hinge the wiki to the numbering of various scenario, requirements, implementation pointers

What you should do?

  - Do your own research based on the documents provided + web search.
  - Rigourously challenge, refine, and improve the structure to incorporate in the wiki.
  - Pay heed to the comments: <!-- ADD MORE OPTIONS -->, <!-- CUSTOM INSTRUCTIONS -->, <!-- DATA PIPELINE --> etc
  - Wherever scenario options are presented, all scenarios / requirements / implementation and their cross combinations need to considered, except when <!-- FIX SCENARIO: <Scenario> --> term is explicitly mentioned.
  - Do NOT blindly make cross combinations - rather break down the problem into isolated + decoupled sub-problems. 
  - Then consider how different choices in one scenario may or may not affect choices in other scenario / requirement / implementation.

## Scenario

The architecture documentations need to be separately documented with related links of commonality between architectures.

### S1: User facing Web application

Here we have 2 options:

- `S1.1`: The website is deployed using CGMs like wordpress, shopify etc
- `S1.2`: The website is custom built using a typical web application stack
- <!-- ADD MORE OPTIONS -->

This context is important to 

- define how clickstream events can be collected. For e.g.
  - `S1.1`: shopify / wordpress may have plugins to incorporate GTM headers, google tags etc
  - `S1.2`: but a custom website framework may need to include GTM installation in a different way
  - <!-- ADD MORE OPTIONS -->
- define how chatbot integration can work 
  - `S1.1`: chatbot can be a custom js code as a wordpress plugin
  - `S1.2`: integration is custom for the usecase
  - <!-- ADD MORE OPTIONS -->


We will consider both the options to document various architecture options.

### S2: Custom Web application deployment 

IF `S1.1`: if the website is a custom web application, then there can be following scenarios:

- `S2.1`: Deployed on AWS
- `S2.2`: Deployed on non AWS
- <!-- ADD MORE OPTIONS -->

<!-- FIX SCENARIO: `S1.1` --> 


The details of full stack web application are less important. A high level overview is fine for developing the overall architecture. Rather we will focus more on: 
  - ingestion of clickstream events -> aggregation -> reporting / visualization
  - integration of aws agentcore chatbot in the web application
  - instrumenting telemetry for aws agentcore chatbot -> cloudwatch -> aggregation -> reporting / visualization


### S3: Chatbot integration in web application

- `S3.1`: Integrated as part of the website
- `S3.2`: Standalone applicated with dedicated domain

<!-- FIX SCENARIO: `S3.1` --> 

### S4: Clickstream Events Collection

- `S4.1`: Google Tag Manager
- `S4.2`: Custom SDKs like AWS clickstream SDK, Hightouch, Adobe, Google Analytics, etc
- <!-- ADD MORE OPTIONS -->


### S5: User Behaviour Analysis Platforms

- `S5.1`: Google Analytics (GA4)
- `S5.2`: Meta Pixel
- `S5.3`: Google Ads
- `S5.4`: CDPs
  - Insider
  - Salesforce
  - Amplitude
- <!-- ADD MORE OPTIONS -->

Why is this important? (Examples)

- The user behaviour analysis needs to be correlated with chatbot adoption + agent performance
- The user behaviour analysis platforms will hold granular events as well as aggregated reports
- Sources like GA4, natively, only stream to BigQuery, and may only offer aggregated reports output
- Sources like Insider, may offer granular event export to AWS: S3 -> REDSHIFT


### S6: GTM Client side vs Server side

- `S6.1`: Client side GTM
- `S6.2`: Server side GTM

Consider both scenarios

### <!-- Add more Scenarios -->

## Requirements

### R1: Analysis of general website clickstream events from frontend

- The general website events need to be collected, stored, analyzed, and metrics visualized to understand user engagement

### R2: Analysis of chatbot specific clickstream events from frontend

- Chatbot specific events need to be collected, stored, analyzed, and metrics visualized to understand chatbot adoption
- Should be correlated with `R1` (general website clickstream events)

### R3: Agent Performance Observability

- AWS AgentCore observability events, and metrics collection to analyze agent performance
- Define custom agent performance metrics. For e.g.
  - number of times guardrail hit
  - number of tool calls per request
  - number of knowledge source fail to fetch errors
  - etc
- Adding custom otel instrumentation code in agent application
- Instrumenting AWS Agentcore runtime, memory, gateway et
- Instrument Agentic App -> collect -> cloudwatch -> metrics / events -> grafana
- Should be correlated with `R1` (general website clickstream events), `R2` (chatbot specific clickstream events)

### R4: Chatbot adoption dashboard

Answer questions like (for e.g.)
- The chatbot adoption metrics were low today because the agent was unable to fetch knowledge documents and kept on returning default responses
  - Low "chatbot session duration clickstream events" + High "number of knowledge source fail to fetch errors"

There can be two options:

- `R4.1`: Separate dashboards for clickstream events: `R1` (general website clickstream events), `R2` (chatbot specific clickstream events) and agent observability metrics: `R3` (agent performance observability)
- `R4.2`: A single unified metrics + visualization dashboard with `R1` (general website clickstream events), `R2` (chatbot specific clickstream events), `R3` (agent performance observability)


### R5: Adoption metrics refresh frequency

- `R5.1`: Batch
- `R5.2`: Realtime

Consider both requirement scenarios, and create architecture variants accordingly.

### R6: Correlation Granularity of User website interaction metrics with Agent Performance

- `R6.1`: Aggregate level: aggregated summaries (for e.g. time) are analyzed. Session wise per user analysis is not possible.
  - The chatbot adoption metrics were low **today** because the agent was unable to fetch knowledge documents
- `R6.2`: Session level: Session wise per user detailed deepdives is possible.
  - Example deepdives of traces, agent behaviour to link with user interaction on other parts of the website
  - This likely won't ever be needed. The agent behaviour for a particular session / user can still be deepdived in an isolated manner on cloudwatch traces


### <!-- Add more Requirements -->

## Implementation

### I1: Chatbot interaction clickstream events

- `I1.1`: Google Tag Manager
  - Custom tags with google tag manager
- `I2.2`: Custom SDKs
  - Add custom SDK "instrumentation" code for events like "chatbot_icon_clicked", "chatbot_session_start" etc
- <!-- Add more implementation options -->

### I2: Chatbot Adoption Metrics Aggregation

- `I2.1`: Metrics defined and aggregated in CDP / User engagement analytics platform
- `I2.2`: Custom Aggregation through granular events


### I3: Chatbot engagement event capture

IF `I2.2`: If event 

- `I3.1`: CDP / Analytics platform event export
  - e.g. Insider CDP -> S3 -> Redshift
- `I3.2`: Clickstream SDKs
  - Direct capture to AWS sink (MSK) -> S3 -> Redshift


### I4: Clickstream ingestion

<!-- DATA PIPELINE -->

Architecture(s) to ingest + process the clickstream events data to AWS based on various scenario, requirement choices.

For e.g.

MSK, Firehose (+ Lambda), Kinesis Data Stream -> S3 / other sinks

### I5: Clickstream processing

<!-- DATA PIPELINE -->

For streaming
- E.g. Apache Flink (or other AWS services) for streaming processing -> Redshift (?)

For batch
- E.g. EMR -> Redshift

### I6: AgentCore deployment

- Defining template agentic architecture deployed on AgentCore. 
- Some basic context setup is needed to create the wiki

### I7: AgentCore custom instrumentation


In terms of actually implementing, I would consider the following 3 things:

1. Evaluation in Dev vs Monitoring in PROD
 - Agent performance metric definitions will vary depending on this

2. Define and categorize metrics - depends alot on your specific usecase + architecture
  - <agent specific>, reasoning/planning, tools, memory, knowledge retrieval etc

3. Add instrumentation - depends on the complexity of the metrics
 - Option 1: Compute metrics from existing traces
 - Option 2: Add manual instrumentation: metrics, traces, or baggage 


Depending on complexity of the metrics, one of 2 things would need to be done:


1. Compute metrics from existing traces
  - Would need to configure auto + native instrumentation (in your frameworks) on AgentCore
  - This should add a lot of interesting attributes into your spans, which are useful to aggregate to metrics in cloudwatch
  - E.g. avg token consumption per planner turn (using gen_ai.usage.input_tokens), 95th perc model inference latency (using span duration), routing failure rates grouped by model type / agent type etc

2. OR Add manual instrumentation: metrics, traces, or baggage - using either opentelemetry sdk or specialized (openllmetry)
  - Can be SRE heavy if you want complex tracing across services
  - E.g. various forms of loop detection (repeat wrong tool calls), RAG retrieval chunk relevancy metrics




Infra vs Agent Application Specific
e.g. Latency is due to a tool call (Lambda function) taking too long vs a Planning agent churning out too many thinking tokens

AgentCore provided
i.e. see metrics specific to Runtime, Memory, Gateway etc - these 

Custom tracing for custom metrics vs use existing auto instrumentation in framework
Former = add instrumentation using OTEL SDKs + tag each trace with relevant flags for easier metric creatio (e.g. add num_tokens etc)
In both cases, you use Cloudwatch to define your metrics

Development vs Production
Evaluate during development (can have manually labelled ground truth)
Monitor during production (can be "indicators" as well as LLM as a judge)

Realtime vs Delayed Eval
You can have Async (e.g. run LLM as a judge) or Batched Eval (as you generate ground truth labels in PROD)
Some managed observability backends do this for you and add extra metrics such as fairness, completeness, toxicity etc (for e.g. Databricks agentbricks)

### I8: AgentCore instrumentation backends

- `I7`: Introduces custom otel sdk instrumentation in application code
- Information gets routed to cloudwatch (logs, metrics, traces)
- what other services does cloudwatch use under the hood (i..e logs = opensearch, x-ray for traces/spans, managed service for metrics (prometheus)?)
- Integrate other backends (?) (langfuse, arize etc)