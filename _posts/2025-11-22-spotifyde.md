---
title: "Spotify Data Pipeline"
description: "Built a scalable azure data platform with dynamic, metadata driven ingestion, incremental cdc pipelines, and databricks silver gold transformations using autoloader, unity catalog, and delta live tables"
date: 2025-11-22 10:00:00 +0530
categories: [Data-Engineering]
tags: [sql, datafactory, databricks, pyspark, dlt]
comments: false
---

In the world of Data Engineering, writing a pipeline that moves data from Point A to Point B is easy. Writing a pipeline that scales to 100+ tables without needing 100+ manual changes? That’s the real challenge.

For my latest project, I built an end-to-end data solution using __Azure Data Factory (ADF)__, __Azure Data Lake Gen2__, and __Databricks (Unity Catalog)__. The goal wasn't just to move Spotify data; it was to build a framework that is __resilient__, __automated__, and __metadata-driven.__


__Architecture Diagram__

<img src="/assets/img/Architecture_Spotify.png" 
     alt="Data Architecture" 
     style="width: 100%; height: auto; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.15);">

__Tech Stack__: Azure Data Factory, SQL, ADLS Gen2, Databricks (PySpark, DLT, Unity Catalog), Logic Apps, GitHub.

### __Phase 1: The Ingestion Engine (Azure Data Factory)__

I needed to ingest data from an __Azure SQL Database__ (simulating a transactional system) to the Data Lake (Bronze Layer). 
The Wrong Way: Creating 20 different pipelines for 20 different tables. 
The "Smart" Way: A single, Metadata-Driven Pipeline.

##### __1. The Metadata-Driven Approach__

Instead of hardcoding table names, I hosted a `loop_input.json` configuration file on GitHub.

- __Lookup Activity__: ADF fetches this JSON via an HTTP Linked Service.
- __ForEach Loop__: The pipeline iterates through every table defined in the config.

This means if I need to add a new table, I don't touch the ADF pipeline code. I simply update the JSON file on GitHub.


##### __2. Incremental Loading (The "High Watermark" Strategy)__

Full loads are expensive. I implemented a robust __CDC (Change Data Capture)__ logic using a watermark approach:

- __Lookup Last CDC__: The pipeline reads a `cdc.json` file from the Data Lake to find the timestamp of the last successful run.
- __Dynamic Querying__: I injected dynamic SQL into the Copy Activity:

```
SELECT * FROM @{item().schema}.@{item().table} 
WHERE @{item().cdc_col} > '@{activity('last_cdc').output.value[0].cdc}'
```
- __Handling Backfill__: I added logic to check if a `from_date` parameter exists. If it does, the system ignores the watermark and performs a historical backfill.

##### __3. Self-Healing State Management__

To update the watermark for the next run, I calculated the `MAX(timestamp)` from the source data.

- __Optimization__: I added an __If Condition__ `(@greater(dataRead, 0))`. The watermark only updates if new data was actually read.
- __Cleanup__: If zero rows are read, a __Delete Activity__ removes the empty Parquet files to keep the lake clean.


### __Phase 2: Governance & Compute (Databricks Unity Catalog)__

Once the data hit the Data Lake (Bronze), I used __Azure Databricks__ for transformation. Instead of legacy mounting, I utilized __Unity Catalog__ for modern governance.

##### __1. Secure Access (No Keys)__
I set up an __Access Connector for Databricks__ with a __Managed Identity__. This allowed Databricks to talk to __ADLS Gen2__ without hardcoding access keys or SAS tokens in notebooks.

##### __2. The Medallion Architecture__

- __Bronze__: Raw Parquet files ingested by ADF.
- __Silver (Cleansing)__: I used __Autoloader__ to ingest raw files. I built a reusable Python class `(transformations.py)` to handle routine tasks like dropping `_rescued_data` and deduping records.
- __Gold (Aggregation)__: Business-level aggregates.

##### __3. Delta Live Tables (DLT) & Quality__
For the Gold layer, I implemented __Delta Live Tables__. This allowed me to define __Data Quality Expectations__

```
@dlt.expect_all_or_drop({"valid_id": "id IS NOT NULL"})
```

If a record breaks the rules, it is dropped immediately, ensuring the analysts only see clean data.


### __Phase 3: DevOps & Monitoring__

A pipeline isn't "production-ready" until it's automated and monitored.

##### __1. Databricks Asset Bundles (DAB)__

I moved away from manual notebook deployment. I initialized a project using `databricks bundle init`.

- Defined infrastructure in `databricks.yml`.
- Deployed separate environments (Dev) via CLI:

```
databricks bundle deploy --target dev
```

This brings CI/CD practices directly into the data workflow.

##### __2. Alerting with Logic Apps__

If the ADF pipeline fails, silence is dangerous.

- I created an __Azure Logic App__ triggered by an HTTP WebHook.
- On pipeline failure, ADF sends a JSON payload (Pipeline Name, Run ID) to the Logic App.
- The Logic App automatically formats and sends a generic email alert to the support team.


### Conclusion

This project started as a simple ETL task but evolved into a masterclass on Azure Data Engineering patterns.

- We decoupled configuration from code (Metadata-driven).
- We secured data access (Unity Catalog).
- We automated deployment (DABs).

Building pipelines is easy. Building pipelines that maintain themselves is where the real engineering happens.


Connect me on [Threads](https://www.threads.com/@bhaveshharmalkar)  [Linkedin](https://www.linkedin.com/in/bhaveshharmalkar/)