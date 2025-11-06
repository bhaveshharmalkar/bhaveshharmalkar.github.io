---
title: "Data Warehouse and Analytics Project"
description: "Comprehensive data warehousing and analytics project, from building a data warehouse to generating actionable insights"
date: 2025-11-04 10:00:00 +0530
categories: [Data-Engineering]
tags: [sql, datascience, dataanalysis]
comments: false
---

#### Problem Addressed

Companies without a proper data management system often face slow, manual, and error-prone reporting processes, leading to outdated or inconsistent data. A data warehouse solves this by providing a single point of truth for all analyses and reports.

#### The Goal

Develop a modern data warehouse using SQL Server to consolidate sales data, enabling analytical reporting and informed decision making, all while providing clear documentation of the data model.

### Project Overview

Project Involves:
1. **Data Architecture**: Designing a Modern Data Warehouse Using Medallion Architecture Bronze, Silver, and Gold layers.
2. **ETL Pipelines**: Extracting, transforming, and loading data from source systems into the warehouse.
3. **Data Modeling**: Developing fact and dimension tables optimized for analytical queries.
4. **Analytics & Reporting**: Creating SQL-based reports and dashboards for actionable insights.

### Phase 1: Data Architecture

Designing a Modern Data Warehouse Using Medallion Architecture Bronze, Silver, and Gold layers.

<img src="/assets/img/data_architecture.png" 
     alt="Data Architecture" 
     style="width: 100%; height: auto; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.15);">

- **Bronze Layer**: Stores raw data as-is from the source systems. Data is ingested from CSV Files into SQL Server Database. In this layer I create schema for the data and then load csv files into it as raw data. 
- **Silver Layer**: This layer includes data cleansing, standardization, and normalization processes to prepare data for analysis. I create a stored procedure for easy to use of sql query. First we truncate the data then add our data into the table for freshness.
- **Gold Layer**: Houses business-ready data modeled into a star schema required for reporting and analytics. In this particular layer I create a fact and dimentions tables. 

Access the require scripts from [here](https://github.com/bhaveshharmalkar/sql-data-warehouse-project/tree/main/scripts).


### Phase 2: ETL Pipeline

Building an automated ETL (Extract, Transform, Load) pipeline to move data efficiently through the Medallion layers from raw ingestion to analytics-ready datasets.

- **Extract**: Data is extracted from multiple CSV sources stored locally and ingested into the Bronze Layer using SQL Server scripts. These scripts define the schema and automate the data import process to ensure consistency across different datasets.
- **Transform**: In the Silver Layer, transformation logic is applied to clean and standardize the data. This includes handling missing values, data type conversions, and creating relationships between tables. I used stored procedures to automate truncation and reloading of tables, ensuring fresh and accurate data for each run.
- **Load**: Finally, in the Gold Layer, the transformed data is loaded into fact and dimension tables following the Star Schema model. These tables are optimized for analytical queries and Power BI dashboards, enabling faster insights and efficient reporting.

### Phase 3: Data Modeling

Designing a Star Schema data model to structure the Gold Layer for efficient reporting and analytics. This model simplifies querying by separating business processes (facts) from descriptive attributes (dimensions).

<img src="/assets/img/data_model.png" 
     alt="Data Model" 
     style="width: 100%; height: auto; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.15);">

- **Fact Table** - `gold.fact_sales`:
Contains measurable, quantitative data about business transactions such as `sales_amount`, `quantity`, and `price`. It also includes foreign keys linking to the product and customer dimension tables.

- **Dimension Table** - `gold.dim_products`:
Stores product-related attributes like `product_name`, `category`, `subcategory`, and `product_line`. This enables detailed product-level analysis and filtering in Power BI reports.

- **Dimension Table** - `gold.dim_customers`:
Includes customer-centric attributes such as `first_name`, `last_name`, `gender`, `country`, and `marital_status`, allowing demographic segmentation and customer behavior analysis.