---
title: "Spotify Data Pipeline"
description: "Built a scalable Azure data platform with dynamic, metadata driven ingestion, incremental CDC pipelines, and Databricks silver gold transformations using Autoloader, Unity Catalog, and Delta Live Tables"
date: 2025-11-22 10:00:00 +0530
categories: [Data-Engineering]
tags: [sql, datafactory, databricks, pyspark, dlt]
comments: false
---

In the world of Data Engineering, writing a pipeline that moves data from Point A to Point B is easy. Writing a pipeline that scales to 100+ tables without needing 100+ manual changes? That’s the real challenge.

For my latest project, I built an end-to-end data solution using __Azure Data Factory (ADF)__, __Azure Data Lake Gen2__, and __Databricks (Unity Catalog)__. The goal wasn't just to move Spotify data; it was to build a framework that is __resilient__, __automated__, and __metadata-driven.__

Here is the architectural breakdown of how I built a scalable, incremental loading system.