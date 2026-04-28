# Azure-Synapse-SSIS-Data-Engineering-Project-End-to-End-Sales-Analytics-Pipeline

## SSIS ETL Workflow Description
## 🔄 SSIS ETL Pipeline – End-to-End Workflow
<img width="1184" height="663" alt="image" src="https://github.com/user-attachments/assets/2184f2a4-24d7-453e-ba2e-bbfcea6e7540" />


### 📌 Overview

In this project, I built an ETL pipeline using SQL Server Integration Services (SSIS) to process sales data from raw files into a structured data warehouse.

The pipeline follows a clear flow:

* Load data into staging (incrementally)
* Build dimension tables
* Load the fact table (incrementally)

In this project, I built an **ETL pipeline using SQL Server Integration Services (SSIS)** to process sales data from raw files into a structured data warehouse.

After processing data through the SSIS pipeline, all cleaned and transformed data is stored in a **SQL Server database**, which serves as the data warehouse layer.
This database is designed using a star schema, enabling efficient querying and analytics.
<img width="356" height="351" alt="image" src="https://github.com/user-attachments/assets/435a629b-ea06-40dc-9721-c0bac09727de" />


---

## 🧭 Pipeline Flow

```text
Raw CSV Files (Sales, Store, Product)
        ↓
Staging Layer (Incremental Load)
        ↓
Dimension Tables (Full Load)
        ↓
Fact Table (Incremental Load)
```

---

## 🟢 Step 1 – Staging Layer (Incremental Load)

The pipeline starts by loading data into staging tables.

Instead of reloading everything, I implemented an **incremental loading strategy**, meaning only new or updated data is processed.

### What happens here:

* Read data from CSV files
* Load into staging tables (`stg_product`, `stg_store`, `stg_sales`)
* Only insert new records based on date for stg_sales. For stg_products and stg_store applied full load

### Why:

* Improves performance
* Avoids duplicate processing
* Scales better with large datasets

---

## 🔵 Step 2 – Dimension Tables (Full Refresh)

After staging, the pipeline builds dimension tables.

### Flow:

* Truncate existing dimension tables
* Reload data from staging

### Tables:

* `dim_product`
* `dim_store`
* `dim_date`

### Why full load:

* Dimensions are relatively small
* Simpler logic and easier maintenance

---

## 🟣 Step 3 – Data Transformation

Inside Data Flow Tasks, several transformations are applied:

* Data type conversion (string → int, date, etc.)
* Data cleaning (trim, handle nulls)
* Derived columns (e.g., calculated fields)
* Lookups to match dimension keys

This ensures the data is clean and consistent before loading into the warehouse.

---

## 🔴 Step 4 – Fact Table (Incremental Load)

The final step is loading the fact table (`fact_sales`).

### Key logic:

* Only new transactions are inserted ( new records in last 3 days)
* Existing records are not reloaded

### My approach:

* Delete records from the last 3 days and reload them with new data.

### Why:

* Prevent duplicate data
* Reduce processing time
* Mimic real-world data pipelines

---

## 🧱 Data Model (Star Schema)

<img width="1419" height="555" alt="image" src="https://github.com/user-attachments/assets/612064fe-7f0a-47a3-aa3a-95b263ad6026" />


* Fact table contains transactional data (sales) and metrics for analyzing
* Dimension tables provide descriptive context

---

## ⚙️ ETL Strategy Summary

* Incremental load for staging tables
* Full refresh for dimension tables
* Incremental load for fact table
* Sequential execution to maintain data integrity

