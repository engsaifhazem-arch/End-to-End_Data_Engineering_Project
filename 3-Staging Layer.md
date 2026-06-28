#  1. Overview

The Staging Layer is the intermediate zone between raw data (Landing Layer) and the final Data Warehouse. Think of it as **building the shelves before stacking the goods.**

| Concept | Analogy |
| :--- | :--- |
| **Landing Layer** | Raw materials delivered to the warehouse. |
| **Staging Layer** | Empty shelves, labeled and organized. |
| **ETL Pipeline** | Forklifts moving materials onto the shelves. |
| **Data Warehouse** | Final organized storage for reporting. |

**What we built:**
- 8 empty tables to hold cleaned data
- Indexes for fast lookups
- A validation stored procedure for quality checks

**What we did NOT do yet:**
- Load any actual data into the tables

---

## 2. Tables Created

We created 7 staging tables (one for each CSV file) plus 1 validation table.

### 2.1 Orders Table (`stg_orders`)

**Purpose:** Holds order data with calculated delivery metrics.

| Column | Description |
| :--- | :--- |
| `order_id` | Unique order identifier |
| `customer_id` | Customer who placed the order |
| `order_status` | Status (delivered, canceled, etc.) |
| `order_purchase_timestamp` | When the order was placed |
| `order_delivered_customer_date` | When it was actually delivered |
| `order_estimated_delivery_date` | When it was supposed to be delivered |
| `delivery_time_days` | Calculated delivery time in days |
| `is_late_delivery` | Flag indicating if delivery was late |

---

### 2.2 Order Items Table (`stg_order_items`)

**Purpose:** Holds individual products within each order.

| Column | Description |
| :--- | :--- |
| `order_item_key` | Surrogate key (auto-generated) |
| `order_id` | Links to the order |
| `product_id` | Links to the product |
| `seller_id` | Links to the seller |
| `price` | Product price |
| `freight_value` | Shipping cost |

---

### 2.3 Payments Table (`stg_order_payments`)

**Purpose:** Holds payment installments for each order.

| Column | Description |
| :--- | :--- |
| `payment_key` | Surrogate key (auto-generated) |
| `order_id` | Links to the order |
| `payment_sequential` | Installment number |
| `payment_type` | Payment method |
| `payment_value` | Value of this installment |

---

### 2.4 Reviews Table (`stg_order_reviews`)

**Purpose:** Holds customer reviews.

| Column | Description |
| :--- | :--- |
| `order_id` | Links to the order |
| `review_score` | Rating (1-5), NULL if no review |
| `has_review` | Flag indicating if a review exists |
| `review_comment_message` | Customer's text comment |

---

### 2.5 Customers Table (`stg_customers`)

**Purpose:** Holds customer data enriched with location.

| Column | Description |
| :--- | :--- |
| `customer_id` | Unique per order (not unique per customer) |
| `customer_unique_id` | True unique customer identifier |
| `customer_zip_code_prefix` | Zip code prefix |
| `customer_city` | City name (enriched) |
| `customer_state` | State code (enriched) |

---

### 2.6 Sellers Table (`stg_sellers`)

**Purpose:** Holds seller data enriched with location.

| Column | Description |
| :--- | :--- |
| `seller_id` | Unique seller identifier |
| `seller_zip_code_prefix` | Zip code prefix |
| `seller_city` | City name (enriched) |
| `seller_state` | State code (enriched) |

---

### 2.7 Products Table (`stg_products`)

**Purpose:** Holds product catalog with translated categories.

| Column | Description |
| :--- | :--- |
| `product_id` | Unique product identifier |
| `product_category_name` | Category name in Portuguese |
| `category_name_english` | Category name in English |
| `product_weight_g` | Weight in grams |
| `product_length_cm` | Length in cm |
| `product_height_cm` | Height in cm |
| `product_width_cm` | Width in cm |

---

### 2.8 Validation Errors Table (`stg_validation_errors`)

**Purpose:** Logs data quality issues found during validation.

| Column | Description |
| :--- | :--- |
| `error_id` | Auto-generated error ID |
| `order_id` | Order with the issue |
| `validation_rule` | Which rule was violated |
| `expected_value` | Expected value |
| `actual_value` | Actual value found |
| `error_message` | Human-readable error description |
| `check_timestamp` | When the error was detected |

---


## 3. Validation Stored Procedure

**What it does:**
1. Adds up all product prices in `stg_order_items` per order
2. Adds up all payments in `stg_order_payments` per order
3. Compares the two totals
4. If they don't match (within $0.01), logs the error in `stg_validation_errors`

**Why we built it:**
- Ensures data consistency between items and payments
- Catches mismatches before they reach the Data Warehouse
- Provides an audit trail for data quality issues

**How it works:**
```sql
-- Simplified logic
IF total_items != total_payments (within $0.01)
    INSERT INTO stg_validation_errors(...)
```

---

## 4. Why We Built This Way

| Decision | Why |
| :--- | :--- |
| **Used `NVARCHAR` for IDs** | Supports special characters and international data |
| **Used `DECIMAL` for money** | Exact precision for financial calculations (unlike `FLOAT`) |
| **Created indexes now** | Faster to build on empty tables than on millions of rows |
| **Separate validation table** | Logs errors without corrupting main data |
| **Stored procedure in T-SQL** | Faster than SSIS for aggregate calculations |

---

## 5. Summary Table

| Object                  | Purpose                   |
| :---------------------- | :------------------------ |
| `stg_orders`            | Store cleaned order data  |
| `stg_order_items`       | Store cleaned order items |
| `stg_order_payments`    | Store cleaned payments    |
| `stg_order_reviews`     | Store cleaned reviews     |
| `stg_customers`         | Store cleaned customers   |
| `stg_sellers`           | Store cleaned sellers     |
| `stg_products`          | Store cleaned products    |
| `stg_validation_errors` | Log validation errors     |
| **Validation SP**       | Check data consistency    |

---


## 6. Technologies Used

| Tool | Purpose |
| :--- | :--- |
| **SQL Server** | Database engine |
| **SSMS** | Writing and executing T-SQL scripts |
| **T-SQL** | Creating tables, indexes, and stored procedures |

---

