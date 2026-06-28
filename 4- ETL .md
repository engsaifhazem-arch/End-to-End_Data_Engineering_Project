#  Olist Data Engineering Project – ETL Documentation

## 1. ETL Overview

The ETL (Extract, Transform, Load) process moves data from raw CSV files (Landing Layer) into staging tables. Each table has its own Data Flow Task with specific transformations.


### 1.1 ETL Pattern by Table

| Table                | ETL Approach                  |
| :------------------- | :---------------------------- |
| `stg_orders`         | SSIS Data Flow + Calculations |
| `stg_order_items`    | SSIS Data Flow (Direct Load)  |
| `stg_order_payments` | SSIS Data Flow (Direct Load)  |
| `stg_order_reviews`  | SSIS Data Flow + Sort         |
| `stg_customers`      | T-SQL (BULK INSERT + JOIN)    |
| `stg_sellers`        | T-SQL (BULK INSERT + JOIN)    |
| `stg_products`       | SSIS Data Flow + SQL Lookup   |


## Screenshot from SSIS
![[Pasted image 20260628204113.png]]


---

## 2. `stg_orders` ETL

### 2.1 Overview

| Property | Value |
| :--- | :--- |
| **Package** | `LoadStaging.dtsx` |
| **Data Flow Task** | `DFT_LoadOrders` |
| **Source** | `olist_orders_dataset.csv` |
| **Destination** | `stg_orders` |

### 2.2 Control Flow

```
[SQL_TruncateOrders] → [DFT_LoadOrders]
```

| Task | Purpose |
| :--- | :--- |
| `SQL_TruncateOrders` | Empties `stg_orders` before loading |
| `DFT_LoadOrders` | Loads data from CSV to staging |

### 2.3 Data Flow Components

```
Flat File Source → Data Conversion → Derived Column → Conditional Split → Data Conversion (2) → OLE DB Destination
```


## Screenshot from SSIS
![[Pasted image 20260628204225.png]]

---


## 3. `stg_order_items` ETL

### 3.1 Overview

| Property | Value |
| :--- | :--- |
| **Package** | `LoadStaging.dtsx` |
| **Data Flow Task** | `DFT_LoadOrderItems` |
| **Source** | `olist_order_items_dataset.csv` |
| **Destination** | `stg_order_items` |

### 3.2 Control Flow

```
[SQL_TruncateOrderItems] → [DFT_LoadOrderItems]
```

### 3.3 Data Flow Components

```
Flat File Source → OLE DB Destination
```


## Screenshot from SSIS


![[Pasted image 20260628204528.png]]


---

## 4. `stg_order_payments` ETL

### 4.1 Overview

| Property | Value |
| :--- | :--- |
| **Package** | `LoadStaging.dtsx` |
| **Data Flow Task** | `DFT_LoadOrderPayments` |
| **Source** | `olist_order_payments_dataset.csv` |
| **Destination** | `stg_order_payments` |

### 4.2 Control Flow

```
[SQL_TruncateOrderPayments] → [DFT_LoadOrderPayments]
```

### 4.3 Data Flow Components

```
Flat File Source → OLE DB Destination
```


## Screenshot from SSIS


![[Pasted image 20260628204609.png]]



---

## 5. `stg_order_reviews` ETL

### 5.1 Overview

| Property | Value |
| :--- | :--- |
| **Package** | `LoadStaging.dtsx` |
| **Data Flow Task** | `DFT_LoadOrderReviews` |
| **Source** | `olist_order_reviews_dataset.csv` |
| **Destination** | `stg_order_reviews` |

### 5.2 Control Flow

```
[SQL_TruncateOrderReviews] → [DFT_LoadOrderReviews]
```

### 5.3 Data Flow Components

```
Flat File Source → Data Conversion → Derived Column → Sort → OLE DB Destination
```




## Screenshot from SSIS
![[Pasted image 20260628204657.png]]



![[Pasted image 20260628204635.png]]


---

## 6. `stg_customers` ETL (T-SQL Approach)

### 6.1 Overview

| Property        | Value                         |
| :-------------- | :---------------------------- |
| **Approach**    | T-SQL                         |
| **Source**      | `olist_customers_dataset.csv` |
| **Destination** | `stg_customers`               |

### 6.3 Process Flow

```mermaid
graph LR
    A[CSV File] --> B[BULK INSERT]
    B --> C[stg_customers_raw]
    C --> D[LEFT JOIN with geolocation_lookup]
    D --> E[stg_customers]
```


## Screenshot from SSIS

![[Pasted image 20260628204721.png]]



---

## 7. `stg_sellers` ETL (T-SQL Approach)

### 7.1 Overview

| Property        | Value                       |
| :-------------- | :-------------------------- |
| **Approach**    | T-SQL only **(no SSIS)**    |
| **Source**      | `olist_sellers_dataset.csv` |
| **Destination** | `stg_sellers`               |

### 7.2 Process Flow

```mermaid
graph LR
    A[CSV File] --> B[BULK INSERT]
    B --> C[stg_sellers_raw]
    C --> D[LEFT JOIN with geolocation_lookup]
    D --> E[stg_sellers]
```






---

## 8. `stg_products` ETL

### 8.1 Overview

| Property | Value |
| :--- | :--- |
| **Package** | `LoadStaging.dtsx` |
| **Data Flow Task** | `DFT_LoadProducts` |
| **Source** | `olist_products_dataset.csv` |
| **Destination** | `stg_products` |

### 8.2 Approach

Hybrid approach: SSIS for raw load + SQL for enrichment.

### 8.3 Process Flow

```mermaid
graph LR
    A[CSV] --> B[SSIS: DFT_LoadProductsRaw]
    B --> C[stg_products_raw]
    C --> D[SQL: LEFT JOIN with category_translation]
    D --> E[stg_products]
```

### 8.4 Step 1: Load Raw Data (SSIS)

```
Flat File Source → OLE DB Destination
```

- **Connection Manager:** `FF_Products`
- **Destination:** `stg_products_raw`

### 8.5 Step 2: Enrich with SQL

```sql
TRUNCATE TABLE dbo.stg_products;
GO

INSERT INTO dbo.stg_products (
    product_id,
    product_category_name,
    category_name_english,
    product_weight_g,
    product_length_cm,
    product_height_cm,
    product_width_cm
)
SELECT
    p.product_id,
    p.product_category_name,
    ISNULL(t.product_category_name_english, p.product_category_name) AS category_name_english,
    p.product_weight_g,
    p.product_length_cm,
    p.product_height_cm,
    p.product_width_cm
FROM dbo.stg_products_raw p
LEFT JOIN dbo.product_category_translation t
    ON p.product_category_name = t.product_category_name;
```



## Screenshot from SSIS


![[Pasted image 20260628204830.png]]


---

## 9. Summary Table

| Table                | ETL Type       | Key Transformations                  | Tool        |
| :------------------- | :------------- | :----------------------------------- | :---------- |
| `stg_orders`         | SSIS Data Flow | Calculated delivery time, late flag  | SSIS        |
| `stg_order_items`    | SSIS Data Flow | Direct load                          | SSIS        |
| `stg_order_payments` | SSIS Data Flow | Direct load                          | SSIS        |
| `stg_order_reviews`  | SSIS Data Flow | `has_review` flag, duplicate removal | SSIS        |
| `stg_customers`      | T-SQL          | Bulk insert, geolocation enrich      | SSIS+SSMS   |
| `stg_sellers`        | T-SQL          | Bulk insert, geolocation enrich      | SSMS        |
| `stg_products`       | Hybrid         | SSIS raw load + SQL enrich           | SSIS + SSMS |


## Screenshot from SSMS



![[Pasted image 20260628205037.png]]


---


## 10. Technologies Used

| Tool | Purpose |
| :--- | :--- |
| **SSIS** | Data Flow Tasks for most tables |
| **T-SQL** | Bulk insert and enrichment for complex tables |
| **SSMS** | Executing T-SQL scripts |

---

**Documentation prepared by:** Saif  
**Project:** Olist Data Engineering Project  
**Phase:** ETL (Staging Load)  
**Version:** 1.0
