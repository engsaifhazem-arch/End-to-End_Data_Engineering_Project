
## 1. Project Overview

This documentation covers the Semantic Layer built using SQL Server Analysis Services (SSAS) Tabular 2022. The layer provides a unified business view of the Olist data for Power BI reporting.

---

## 2. Data Sources

**Source Database:** OlistDW (SQL Server Data Warehouse)

**Tables Imported:**

| Table | Type | Rows |
| :--- | :--- | :--- |
| DimDate | Dimension | 1,096 |
| DimCustomer | Dimension | 99,441 |
| DimProduct | Dimension | 32,951 |
| DimSeller | Dimension | 3,093 |
| FactOrders | Fact | 112,098 |

**Import Method:** CSV export from SQL Server due to SSDT connection encryption issues. All headers were verified before import.

---

## 3. Table Schema

### DimDate

| Column | Data Type | Description |
| :--- | :--- | :--- |
| DateKey | Integer | Primary key (YYYYMMDD) |
| FullDate | Date | Full date value |
| Year | Integer | Year |
| Month | Integer | Month number |
| MonthName | Text | Month name |
| Quarter | Integer | Quarter number |
| QuarterName | Text | Quarter name |
| DayOfWeek | Integer | Day number |
| DayName | Text | Day name |
| IsWeekend | Boolean | 1 = weekend |

### DimCustomer

| Column | Data Type | Description |
| :--- | :--- | :--- |
| CustomerKey | Integer | Surrogate key |
| CustomerID | Text | Business key |
| CustomerUniqueID | Text | Unique customer identifier |
| CustomerZipCodePrefix | Text | Zip code |
| CustomerCity | Text | City name |
| CustomerState | Text | State code |

### DimProduct

| Column | Data Type | Description |
| :--- | :--- | :--- |
| ProductKey | Integer | Surrogate key |
| ProductID | Text | Business key |
| ProductCategoryName | Text | Portuguese category |
| CategoryNameEnglish | Text | English category |
| ProductWeightG | Decimal | Weight in grams |
| ProductLengthCm | Decimal | Length |
| ProductHeightCm | Decimal | Height |
| ProductWidthCm | Decimal | Width |

### DimSeller

| Column | Data Type | Description |
| :--- | :--- | :--- |
| SellerKey | Integer | Surrogate key |
| SellerID | Text | Business key |
| SellerZipCodePrefix | Text | Zip code |
| SellerCity | Text | City name |
| SellerState | Text | State code |

### FactOrders

| Column | Data Type | Description |
| :--- | :--- | :--- |
| OrderItemKey | Integer | Surrogate key |
| OrderID | Text | Order identifier |
| CustomerKey | Integer | FK to DimCustomer |
| ProductKey | Integer | FK to DimProduct |
| SellerKey | Integer | FK to DimSeller |
| OrderDateKey | Integer | FK to DimDate |
| DeliveryDateKey | Integer | FK to DimDate |
| Price | Decimal | Product price |
| FreightValue | Decimal | Shipping cost |
| ReviewScore | Decimal | Customer rating (1-5) |
| DeliveryTimeDays | Integer | Delivery duration |
| IsLateDelivery | Boolean | 1 = late, 0 = on time |

---

## 4. Relationships

| From | To | Cardinality |
| :--- | :--- | :--- |
| FactOrders[CustomerKey] | DimCustomer[CustomerKey] | Many-to-One |
| FactOrders[ProductKey] | DimProduct[ProductKey] | Many-to-One |
| FactOrders[SellerKey] | DimSeller[SellerKey] | Many-to-One |
| FactOrders[OrderDateKey] | DimDate[DateKey] | Many-to-One |
| FactOrders[DeliveryDateKey] | DimDate[DateKey] | Many-to-One |

---

## 5. DAX Measures

All measures are stored in FactOrders table.

### Revenue KPIs

| Measure | DAX |
| :--- | :--- |
| Total Revenue | `SUM(FactOrders[Price])` |
| Total Freight | `SUM(FactOrders[FreightValue])` |
| Total Orders | `DISTINCTCOUNT(FactOrders[OrderID])` |
| Total Order Items | `COUNTROWS(FactOrders)` |
| Avg Order Value | `DIVIDE([Total Revenue], [Total Orders])` |
| Total Customers | `DISTINCTCOUNT(FactOrders[CustomerKey])` |
| Total Products Sold | `DISTINCTCOUNT(FactOrders[ProductKey])` |

### Delivery KPIs

| Measure | DAX |
| :--- | :--- |
| Avg Delivery Days | `AVERAGE(FactOrders[DeliveryTimeDays])` |
| OnTime Delivery Rate | `DIVIDE(COUNTROWS(FILTER(FactOrders, FactOrders[IsLateDelivery] = 0)), COUNTROWS(FILTER(FactOrders, FactOrders[IsLateDelivery] IN {0,1})))` |
| Late Delivery Rate | `DIVIDE(COUNTROWS(FILTER(FactOrders, FactOrders[IsLateDelivery] = 1)), COUNTROWS(FILTER(FactOrders, FactOrders[IsLateDelivery] IN {0,1})))` |

### Customer Satisfaction KPIs

| Measure | DAX |
| :--- | :--- |
| Avg Review Score | `AVERAGE(FactOrders[ReviewScore])` |
| Review Response Rate | `DIVIDE(COUNTROWS(FILTER(FactOrders, NOT ISBLANK(FactOrders[ReviewScore]))), COUNTROWS(FactOrders))` |

---

## 6. Key Metrics Defined

| Metric | Value | What It Means |
| :--- | :--- | :--- |
| **Total Revenue** | 13.49M BRL | Total sales value |
| **Total Orders** | 98,196 | Number of orders placed |
| **Avg Order Value** | 137.41 BRL | Average spend per order |
| **On-Time Delivery** | 92.2% | Orders delivered on time |
| **Avg Review Score** | 4.03 / 5 | Customer satisfaction rating |
| **Review Response** | 7.82% | % of orders with reviews |
| **Avg Delivery Time** | 12.41 days | Time from order to delivery |




---
## 7. Validation Results

All measures were validated against the Data Warehouse using SQL queries. Expected vs actual values:

| KPI | SQL Value | DAX Value | Status |
| :--- | :--- | :--- | :--- |
| Total Revenue | 13,493,544.30 | 13,493,544.30 |  Pass |
| Total Orders | 98,196 | 98,196 |  Pass |
| Total Order Items | 112,098 | 112,098 |  Pass |
| Avg Order Value | 137.41 | 137.41 |  Pass |
| Avg Delivery Days | 12.41 | 12.41 |  Pass |
| OnTime Delivery Rate | 0.922 | 0.922 |  Pass |
| Avg Review Score | 4.03 | 4.03 |  Pass |
| Review Response Rate | 0.078 | 0.078 |  Pass |

---



## 8. Contact

**Data Engineer:** Saif  
**Project:** Olist Data Engineering Project  
**Date:** 2026-06-28
