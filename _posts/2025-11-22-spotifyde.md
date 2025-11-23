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

### Phase 1: The Ingestion Engine (Azure Data Factory)

I needed to ingest data from an __Azure SQL Database__ (simulating a transactional system) to the Data Lake (Bronze Layer). 
The Wrong Way: Creating 20 different pipelines for 20 different tables. 
The "Smart" Way: A single, Metadata-Driven Pipeline.

1. __The Metadata-Driven Approach__
Instead of hardcoding table names, I hosted a `loop_input.json` configuration file on GitHub.

- __Lookup Activity__: ADF fetches this JSON via an HTTP Linked Service.
- __ForEach Loop__: The pipeline iterates through every table defined in the config.

This means if I need to add a new table, I don't touch the ADF pipeline code. I simply update the JSON file on GitHub.