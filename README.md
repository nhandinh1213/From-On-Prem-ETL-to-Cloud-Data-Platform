# From On-Prem ETL to Cloud Data Platform
This project demonstrates the transition from a traditional on-premise ETL pipeline built with SSIS to a modern cloud-based data ingestion pipeline using Azure Synapse Analytics.
The goal is to show how a legacy data processing workflow can be gradually modernized into a scalable cloud architecture.

## 🏗️ Architecture Evolution
Phase 1 (On-Prem): 
CSV → SSIS → SQL Server (Data Warehouse)

        ↓ Migration

Phase 2 (Cloud): 
CSV → Synapse Pipeline → Data Lake (Silver) → Serverless SQL (Gold)

# 🔵Phase 1- SSIS ETL (On-prem)
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

# 🔵 Phase 2 – Azure Synapse (Cloud Ingestion)
Azure Data Lake Storage Gen2 is used as the central storage layer, organized into multiple containers to support a medallion architecture (Raw → Silver → Gold)

<img width="1907" height="862" alt="image" src="https://github.com/user-attachments/assets/31e19dfa-bceb-462c-94e7-96fe7b3f7dc3" />

* Built a Synapse pipeline
* Loaded raw CSV data into a **Silver layer in Data Lake**

<img width="1571" height="428" alt="image" src="https://github.com/user-attachments/assets/3ca05920-f5aa-4f5c-b3b6-d0bbd872a2fa" />

* Query data from database

<img width="1527" height="780" alt="image" src="https://github.com/user-attachments/assets/84c87da5-380a-4b2c-823f-be3fb3834124" />

* Connect data from Synapse to Power BI for visualization
<img width="954" height="460" alt="image" src="https://github.com/user-attachments/assets/28d01cf6-1a7e-4ea9-9425-92e6ac0811f3" />
<img width="1104" height="878" alt="image" src="https://github.com/user-attachments/assets/439b6129-6441-4d0f-bc61-ee07613f3e6f" />


  
* Pipeline responsibilities:

  * Data ingestion
  * Data movement (Raw → Silver)
* No complex transformation in pipeline
* Analytical logic handled separately using **Serverless SQL**

👉 This reflects a modern ELT-style architecture.
<img width="1415" height="275" alt="image" src="https://github.com/user-attachments/assets/db1ad91d-1fde-43f4-9974-ef8e0fa2ef3b" />


---

## 🧠 Key Differences

| Aspect         | SSIS (On-Prem) | Synapse (Cloud) |
| -------------- | -------------- | --------------- |
| Processing     | ETL            | ELT             |
| Transformation | In pipeline    | In SQL layer    |
| Storage        | SQL Server     | Data Lake       |
| Complexity     | High           | Lightweight     |
| Scalability    | Limited        | Scalable        |

---

## 🔗 How They Connect

* SSIS handles **structured data warehouse modeling**
* Synapse handles **cloud-based ingestion and storage**
* Together, they simulate a **hybrid data platform**

---

## 🚀 Key Takeaways

* Experience with both **legacy (SSIS)** and **modern (Synapse)** data platforms
* Understanding of **ETL vs ELT paradigms**
* Ability to design **data warehouse models and data lake architectures**
* Demonstrated knowledge of **migration mindset (on-prem → cloud)**

---

## 🔮 Future Improvements
* Implement incremental loading in Synapse
* Introduce data partitioning strategy
* Add monitoring and logging



