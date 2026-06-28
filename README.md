#  Olist Data Engineering Project

This repository contains an end-to-end Data Engineering solution for the **Olist Brazilian E-Commerce Public Dataset**. The project demonstrates the full data lifecycle, from raw CSV ingestion to business intelligence reporting, using the **Microsoft BI Stack** – **SQL Server**, **SSIS**, **SSAS**, and **Power BI**. It showcases how raw transactional data is transformed into actionable business insights through a structured Data Warehouse and Semantic Layer.

## Project Overview

The goal of this project is to design, implement, and deploy a complete Data Warehouse solution for Olist, a Brazilian marketplace. The ETL pipelines extract data from 9 CSV files, load it into a staging area, transform it into a Star Schema Data Warehouse, and build a Semantic Layer for consistent reporting. Finally, an interactive Power BI dashboard delivers 10 business KPIs to stakeholders.

### Source Data:
- **9 CSV Files**: Orders, Order Items, Payments, Reviews, Customers, Sellers, Products, Geolocation, and Category Translation.
- **Time Period**: 2016–2018.
- **Total Records**: ~112K order items, ~99K customers, ~33K products.

### Tools and Technologies Used:
- **SQL Server**: Database engine and Data Warehouse storage.
- **SSMS**: Database management and T-SQL scripting.
- **SSIS**: ETL pipeline for staging layer load.
- **SSAS**: Tabular Semantic Layer for business measures.
- **Power BI**: Interactive dashboard and reporting.
- **GitHub**: Version control and documentation.

---

## Architecture Overview

Below is the architecture of the implemented solution:

1. **Landing Layer**:
   - Raw CSV files stored unmodified in `02_Landing/`.
   - Serves as the audit trail and disaster recovery point.

2. **Staging Layer**:
   - 7 staging tables created in SQL Server using T-SQL.
   - Indexes added for performance optimization.
   - Validation stored procedure (`usp_Validate_Staging`) ensures data consistency.

3. **ETL Pipelines (SSIS + T-SQL)**:
   - **SSIS Data Flow** for: Orders, Order Items, Payments, Reviews, Products.
   - **T-SQL Bulk Insert** for: Customers and Sellers (to avoid metadata issues).
   - All tables truncated before each load to ensure fresh data.

4. **Data Warehouse (Star Schema)**:
   - 4 Dimensions: `DimDate`, `DimCustomer`, `DimProduct`, `DimSeller`.
   - 1 Fact Table: `FactOrders` (grain: order line item).
   - Surrogate keys used for performance and stability.
   - Unique constraints prevent duplicates.

5. **Semantic Layer (SSAS Tabular)**:
   - Tabular model with 10+ DAX measures (KPIs).
   - Centralized business logic for consistent reporting.

6. **Power BI Dashboard**:
   - 3-page interactive dashboard connected live to SSAS.
   - Global slicers for date, state, and category.
   - Delivers 10 KPIs including Revenue, Delivery Performance, and Customer Satisfaction.

---

## Project Workflow

### 1. **Business Analysis (BRD & DRD)**
   - Defined 9 primary KPIs across 4 domains: Financial, Logistics, Customer Satisfaction, and Geographic/Seller Insights.
   - Documented grain, source files, validation rules, and target schema.

### 2. **Landing Layer**
   - Stored 9 raw CSV files in `02_Landing/` without any modifications.
   - All files remain read-only as a source of truth.

### 3. **Staging Layer (T-SQL)**
   - Created 7 staging tables (`stg_orders`, `stg_order_items`, `stg_order_payments`, `stg_order_reviews`, `stg_customers`, `stg_sellers`, `stg_products`).
   - Added indexes on foreign keys for performance.
   - Built `usp_Validate_Staging` stored procedure for revenue consistency checks.

### 4. **ETL Pipelines (SSIS + T-SQL)**
   - **SSIS Data Flow Tasks**:
     - `DFT_LoadOrders` – Calculates delivery time and late flag.
     - `DFT_LoadOrderItems` – Direct load.
     - `DFT_LoadOrderPayments` – Direct load.
     - `DFT_LoadOrderReviews` – Creates `has_review` flag, removes duplicates.
     - `DFT_LoadProducts` – Loads raw products, enriched via SQL.
   - **T-SQL Bulk Insert**:
     - `stg_customers` – Enriched with geolocation lookup.
     - `stg_sellers` – Enriched with geolocation lookup.

### 5. **Data Warehouse (Star Schema)**
   - Designed 4 Dimensions and 1 Fact table.
   - Loaded dimensions from staging tables using `INSERT INTO ... SELECT ...`.
   - Loaded `FactOrders` by joining 5 staging tables.
   - Added unique constraints on business keys.

### 6. **Semantic Layer (SSAS Tabular)**
   - Built a Tabular model with DAX measures for all KPIs.
   - Deployed to SSAS server (`localhost\SSAS2022`).
   - Organized measures into Display Folders for Power BI.

### 7. **Power BI Dashboard**
   - Connected live to SSAS Tabular model.
   - Built 3 pages: Overview, Products & Sellers, Logistics & Satisfaction.
   - Applied global slicers for interactive filtering.

---


## Technologies Used

| Tool | Purpose |
| :--- | :--- |
| **SQL Server** | Database engine and Data Warehouse storage |
| **SSMS** | Database management and T-SQL scripting |
| **SSIS** | ETL pipelines for staging layer load |
| **SSAS** | Tabular Semantic Layer for business measures |
| **Power BI** | Interactive dashboard and reporting |
| **GitHub** | Version control and documentation |

---


## Business Outcomes

| KPI | Value |
| :--- | :--- |
| **Total Revenue** | 13.49M BRL |
| **Total Orders** | 98,196 |
| **Avg Order Value** | 137.41 BRL |
| **On-Time Delivery** | 92.2% |
| **Avg Review Score** | 4.03 / 5 |
| **Review Response Rate** | 7.82% |

---


**Project by:** Saif  
 
