# Agent Skill: Data Engineering System Design Expert (Senior/Staff Level)

## Skill Overview
This skill embodies the expertise required to design, critique, and optimize data engineering systems at scale, mirroring the bar for senior roles at top tech companies (Uber, DoorDash, etc.). It equips the agent to navigate ambiguous system design prompts by using a structured 6-step framework, emphasizing trade-offs, scalability, and logical reasoning over simple "correct" answers.

## Core Philosophy & Interview Mindset
*   **Navigate Ambiguity:** There is no single correct architecture. 10 senior engineers will produce 10 valid designs. The goal is to justify choices and explain trade-offs (cost vs. latency, complexity vs. scale).
*   **Make Reasoning Visible:** Always vocalize *why* a specific tool or architecture was chosen.
*   **Wide & Zoom Approach:** Understand the broad system first, then deep-dive into specific areas (e.g., Kafka internals, Spark query plans, Data Modeling) based on constraints.
*   **Never Jump to the Whiteboard:** Do not start drawing boxes (Kafka, Spark, Airflow) without completing Step 1.

---

## The 6-Step System Design Framework

### Step 1: Requirements Gathering (Do Not Skip)
The most underrated step. Clarify the problem before designing.
*   **Functional Requirements (What the system needs to do):**
    *   *Who is the user?* (e.g., Marketing Analyst doing SQL, ML team needing feature stores, Product team monitoring live dashboards).
    *   *What do they need?* (Aggregated metrics, raw event streams, historical snapshots).
    *   *How will they access it?* (SQL queries, BI tools like Tableau, REST APIs).
*   **Non-Functional Requirements (How the system behaves):**
    *   *Latency SLA:* Needs to be fresh in 1 minute (Streaming) vs. 1 hour/1 day (Batch). This single answer changes the entire architecture.
    *   *Volume:* Gigabytes (Pandas/Polars) vs. Terabytes/Petabytes (Distributed compute like Spark + storage optimization).
    *   *Availability:* Tolerance for downtime.
    *   *Data Retention:* Do we keep data forever, or move it to archive after 90 days?

### Step 2: Back-of-the-Envelope Calculations
Use math to justify architectural choices. 
*   **Throughput/Volume Formula:** `Users` × `Sessions/Day` × `Events/Session` = `Total Events/Day`.
*   **Storage Estimation:** `Total Events/Day` × `Average Event Size (e.g., JSON = 700 bytes to 1 KB)`.
*   *Example Application:* 
    *   300M events/day at 700 bytes = ~210 GB/day -> 6.3 TB/month.
    *   Retaining 12 months = ~75 TB raw.
    *   Applying Parquet/Delta compression (2x to 5x) = ~15-38 TB actual storage.
    *   *Decision:* Volume justifies Spark (not single-node) and Parquet/Delta format. Modest queries per second (~500/sec) proves standard Kafka (3 brokers) is sufficient.

### Step 3: Pipeline Design & Architecture
Determine how data moves from Source A to Destination B.
*   **Batch Processing (Cost > Latency):** 
    *   Used when daily/hourly latency is acceptable. 
    *   *Stack:* Spark, Airflow/Prefect/Dagster, S3/ADLS.
*   **Stream Processing (Latency > Cost):** 
    *   Used for real-time dashboards, fraud detection. Latency < 1 minute.
    *   *Stack:* Kafka (Ingestion/Buffer), Spark Structured Streaming / Flink (Processing), Redis / DynamoDB / Cassandra (Serving Layer).
*   **Lambda Architecture (Batch + Streaming):**
    *   Runs parallel paths for the same event stream.
    *   *Speed Layer:* Streams to a low-latency serving database (e.g., Redis).
    *   *Historical Layer:* Buffers data to object storage (e.g., AWS Firehose -> S3) for heavy batch aggregation.
    *   *Use Case:* Uber surge pricing (Live state from streaming + Historical baselines from batch).
*   **Kappa Architecture (Pure Streaming):**
    *   One streaming path for everything. Kafka acts as both buffer and historical storage.
    *   Historical reprocessing is done by replaying Kafka offsets through stream processors.
    *   *Trade-off:* Expensive to store petabytes in Kafka long-term; replaying years of data through a stream processor is slower than batch.

### Step 4: Data Modeling
Do not just draw tables; defend your choices.
*   **Medallion Architecture (Lakehouse Pattern):**
    *   **Bronze:** Raw data stored *as-is* (JSON dumps, CDC logs). Immutable source of truth. Schema on read.
    *   **Silver:** Cleaned, structured, deduplicated, and type-casted. Fact and dimension tables live here.
    *   **Gold:** Pre-aggregated, pre-joined data serving direct business metrics (BI/Dashboards). Fast reads, no complex joins.
*   **Star Schema vs. One Big Table (OBT):**
    *   *Star Schema (Normalized):* Central Fact table with surrounding Dimension tables. Standard for BI, easy to maintain. *Tradeoff:* Heavy joins at query time.
    *   *OBT (Denormalized):* Pre-join everything into wide tables. Excellent for specific, repetitive dashboard queries. *Tradeoff:* Expensive updates (changing a category name means updating millions of rows).
*   **Slowly Changing Dimensions (SCD):**
    *   *Type 1 (Overwrite):* Simple, but destroys historical context.
    *   *Type 2 (Keep History):* Adds `valid_from` and `valid_to` columns. Closes old records and inserts new ones. Essential when historical state matters (e.g., user tier at the time of purchase).
*   **Storage Optimization Strategies:**
    *   *Partitioning:* Usually by `event_date`. Reduces scanned data. *Limitation:* Rigid query patterns required.
    *   *Liquid Clustering / Z-Ordering:* Dynamic file organization eliminating the rigidity of fixed partition columns. 

### Step 5: Storage & File Formats
Select appropriate formats to avoid scanning entire datasets unnecessarily.
*   **Row-based (Avro):** Data is serialized row by row. Perfect for *write-heavy* streaming pipelines (e.g., appending events instantly without memory reorganization).
*   **Column-based (Parquet):** Data is grouped by columns. Perfect for *read-heavy* analytical queries. Allows "Column Pruning" (skipping 198 out of 200 columns) and file skipping via statistics.
*   **Open Table Formats (Delta Lake, Apache Iceberg, Hudi):**
    *   Fixes the issues of raw Parquet dumps in S3 (No ACID, small file problems, missing schema enforcement).
    *   Enables ACID transactions, Time Travel, Schema Evolution, and File Compaction (`OPTIMIZE`).

### Step 6: Data Quality & Observability
Shift quality checks as far left as possible. Do not let bad data fail silently.
*   **The 5 Data Quality Dimensions:**
    1.  *Completeness:* Are fields unexpectedly null? Did row counts drop compared to normal?
    2.  *Accuracy:* Are values logically possible? (e.g., order amount cannot be negative).
    3.  *Consistency:* Does revenue in the Orders table match revenue in the Payments system?
    4.  *Freshness:* Is the data meeting its SLA? (e.g., dashboard says it updates daily, but data is 3 days old).
    5.  *Uniqueness:* Are there duplicates created by pipeline retries?
*   **Data Contracts:** Prevent structural issues at the point of ingestion. A formal agreement between producer (microservice) and consumer (pipeline) managed via a Schema Registry. Stops breaking schema changes *before* they enter Bronze.
*   **Pipeline Observability:** Track DAG runtimes, failure rates, and cluster sizing (Airflow/Datadog metrics).

### Step 7: Scalability, Backfills, & DataOps
Design systems that survive failures and changes.
*   **Idempotency (The Golden Rule):** Running a pipeline 1 time or 100 times with the same input must yield the *exact same output*. Use `MERGE` instead of `INSERT` to handle incremental loads without creating duplicates.
*   **Backfills:** The ability to gracefully reprocess historical data due to bugs or changing business logic. Enabled natively by Open Table Formats (Atomic overwrites of targeted partitions).
*   **Schema Evolution Handling:**
    *   *Be flexible at Bronze:* Use `mergeSchema`. Accept new fields as they appear to preserve raw data safely.
    *   *Be strict at Silver/Gold:* Explicitly map, type-cast, and validate columns. Prevent upstream schema drift from silently breaking BI dashboards.

---

## Actionable Execution Guide for the Agent
When a user asks you to critique or design a data pipeline:
1.  **Enforce Step 1:** Immediately ask for Functional (Who, What) and Non-Functional (Volume, Latency) constraints if missing. 
2.  **Run Math:** Generate a quick back-of-the-envelope calculation regarding TPS (Transactions Per Second) and raw Storage volume to justify your choice of compute (e.g., Kafka vs API polling, Spark vs Pandas).
3.  **Propose Architecture:** Specify Lambda, Kappa, or Batch based strictly on latency SLA. 
4.  **Define the Medallion Layers:** Output concrete examples of what raw schemas look like in Bronze, what Fact/Dim tables live in Silver, and the exact aggregated metrics in Gold.
5.  **Inject Edge Cases:** Actively highlight trade-offs: Ask the user "What happens if a late-arriving event comes in?" or "How will we handle a schema change if the source adds a column?" and provide solutions (e.g., Data Contracts, Idempotent Merges).