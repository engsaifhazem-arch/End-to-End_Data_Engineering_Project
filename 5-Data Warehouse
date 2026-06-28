#  1. What We Built

We transformed the staging tables into a **Star Schema** optimized for reporting.


---

## 2. The Star Schema

```
                    ┌─────────────────┐
                    │    DimDate      │
                    │  (1,096 rows)   │
                    └────────┬────────┘
                             │
┌─────────────────┐          │          ┌─────────────────┐
│   DimCustomer   │──────────┼──────────│   DimProduct    │
│  (99,441 rows)  │          │          │  (32,951 rows)  │
└─────────────────┘          │          └─────────────────┘
                             │
                    ┌────────┴────────┐
                    │   FactOrders    │
                    │  (112,098 rows) │
                    └────────┬────────┘
                             │
┌─────────────────┐          │          ┌─────────────────┐
│    DimSeller    │──────────┼──────────│    DimDate      │
│   (3,093 rows)  │                     │ (Delivery Date) │
└─────────────────┘                     └─────────────────┘
```

---

## 3. The Tables

### 3.1 DimDate – Time Dimension

**Purpose:** Breaks down dates so we can analyze trends by year, month, quarter, or day of week.

| What It Contains | Example |
| :--- | :--- |
| Date key, full date, year, month, quarter, day name, weekend flag | 2018-01-01 → Year 2018, Month 1, Q1, Monday, Not Weekend |

**Row Count:** 1,096 (all days from 2016–2018)

---

### 3.2 DimCustomer – Customer Dimension

**Purpose:** Holds customer information for analysis by location.

| What It Contains | Example |
| :--- | :--- |
| Customer ID, unique customer ID, zip code, city, state | São Paulo, SP |

**Row Count:** 99,441 unique customers

---

### 3.3 DimProduct – Product Dimension

**Purpose:** Holds product catalog with translated categories.

| What It Contains | Example |
| :--- | :--- |
| Product ID, category (Portuguese & English), weight, dimensions | health_beauty → health_beauty |

**Row Count:** 32,951 unique products

---

### 3.4 DimSeller – Seller Dimension

**Purpose:** Holds seller information for analysis by location.

| What It Contains | Example |
| :--- | :--- |
| Seller ID, zip code, city, state | São Paulo, SP |

**Row Count:** 3,093 unique sellers

---

### 3.5 FactOrders – The Core Fact Table

**Purpose:** Holds every product sold in every order. This is the "sales receipt" table.

| What It Contains | Example |
| :--- | :--- |
| Order ID, customer, product, seller, dates, price, freight, review score, delivery metrics | Order 123, Customer A, Product X, Price 50.00, 4.5 stars |

**Grain:** One row per product in an order (order line item).

**Row Count:** 112,098 order items

---

## 4. How We Built It

### 4.1 Approach: T-SQL Only

We used **T-SQL scripts** (no SSIS) to load the Data Warehouse. This was simpler and more reliable for joining multiple staging tables.

### 4.2 The Process

1. **Drop FactOrders** (remove foreign key constraints)
2. **Truncate dimension tables** (empty them for fresh load)
3. **Reload dimensions** from staging tables
4. **Add unique constraints** (prevent duplicates)
5. **Recreate FactOrders** with foreign keys
6. **Load FactOrders** by joining all staging tables

### 4.3 Key Join Logic

The Fact table combines data from multiple staging tables:

```sql
SELECT
    o.order_id,
    c.CustomerKey,
    p.ProductKey,
    s.SellerKey,
    d1.DateKey AS OrderDateKey,
    d2.DateKey AS DeliveryDateKey,
    i.price,
    i.freight_value,
    r.review_score,
    o.delivery_time_days,
    o.is_late_delivery
FROM stg_orders o
INNER JOIN stg_order_items i ON o.order_id = i.order_id
LEFT JOIN stg_order_reviews r ON o.order_id = r.order_id
INNER JOIN DimCustomer c ON o.customer_id = c.CustomerID
INNER JOIN DimProduct p ON i.product_id = p.ProductID
INNER JOIN DimSeller s ON i.seller_id = s.SellerID
INNER JOIN DimDate d1 ON CAST(o.order_purchase_timestamp AS DATE) = d1.FullDate
LEFT JOIN DimDate d2 ON CAST(o.order_delivered_customer_date AS DATE) = d2.FullDate
WHERE o.order_status NOT IN ('canceled', 'unavailable');
```

---

## 5. Verification

### 5.1 Final Row Counts

| Table       | Rows    | Status |
| :---------- | :------ | :----- |
| DimDate     | 1,096   | pass   |
| DimCustomer | 99,441  | pass   |
| DimProduct  | 32,951  | pass   |
| DimSeller   | 3,093   | pass   |
| FactOrders  | 112,098 | pass   |

### 5.2 Quality Checks

| Check                   | Result |
| :---------------------- | :----- |
| NULL foreign keys       | 0      |
| Duplicate business keys | 0      |
| All rows matched        | yes    |

---


## 6. Key Takeaways

| Lesson | Why It Matters |
| :--- | :--- |
| **Star Schema simplifies reporting** | Users don't need complex joins |
| **Surrogate keys improve performance** | Integer joins are faster than text |
| **Unique constraints prevent duplicates** | Keeps data clean |
| **Drop Fact before truncating dimensions** | Removes foreign key conflicts |

---

## 7. Technologies Used

| Tool       | Purpose                                           |
| :--------- | :------------------------------------------------ |
| SQL Server | Database engine                                   |
| SSMS       | Writing and executing T-SQL scripts               |
| T-SQL      | Creating tables, loading data, adding constraints |


---

**Documentation prepared by:** Saif  
**Project:** Olist Data Engineering Project  
**Phase:** Data Warehouse (Star Schema)  
**Version:** 1.0
