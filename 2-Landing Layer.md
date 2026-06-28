#  1. Overview

The Landing Layer is the first stage of the data pipeline. It is a **read-only repository** where raw source files are stored exactly as received from the source system.

**Purpose:**
- Maintain a complete audit trail of the original data
- Enable re-running the ETL pipeline from scratch if a bug is discovered
- Protect against upstream data changes or deletions
- Provide a disaster recovery point for the pipeline

**Rule:** Files in this layer are never modified. No transformations, no column renames, no data type changes.

---

## 2. Directory Structure

```
02_Landing/
├── olist_orders_dataset.csv
├── olist_order_items_dataset.csv
├── olist_order_payments_dataset.csv
├── olist_order_reviews_dataset.csv
├── olist_customers_dataset.csv
├── olist_sellers_dataset.csv
├── olist_products_dataset.csv
├── olist_geolocation_dataset.csv
└── product_category_name_translation.csv
```

---

## 3. Source Files

### 3.1 Orders Dataset

**File:** `olist_orders_dataset.csv`

**Purpose:** Core transactional log. Each row represents one order placed on the Olist platform.

**Key Columns:**

| Column | Description |
| :--- | :--- |
| `order_id` | Unique order identifier. Links to all other order-related tables. |
| `customer_id` | Customer identifier for this order. Note: Same customer may have different IDs across orders. |
| `order_status` | Status of the order (delivered, canceled, shipped, etc.). Used to filter valid orders. |
| `order_purchase_timestamp` | Date and time the order was placed. |
| `order_delivered_customer_date` | Actual delivery date. Used to calculate delivery time. |
| `order_estimated_delivery_date` | Estimated delivery date. Used to determine late deliveries. |

**Engineering Role:** Central table. All other tables connect to this via `order_id`.

---

### 3.2 Order Items Dataset

**File:** `olist_order_items_dataset.csv`

**Purpose:** Line items for each order. An order can contain multiple products.

**Key Columns:**

| Column | Description |
| :--- | :--- |
| `order_id` | Links to Orders dataset. |
| `product_id` | Links to Products dataset. |
| `seller_id` | Links to Sellers dataset. |
| `price` | Price of the individual product. |
| `freight_value` | Shipping cost for this product. |

**Relationship:** One order can have multiple items. The `order_id` appears multiple times in this table.

**Example:**

| order_id | order_item_id | price | freight_value |
| :--- | :--- | :--- | :--- |
| `00143d0f...` | 1 | 21.33 | 15.10 |
| `00143d0f...` | 2 | 21.33 | 15.10 |
| `00143d0f...` | 3 | 21.33 | 15.10 |

- Total product value: `21.33 × 3 = 63.99`
- Total freight: `15.10 × 3 = 45.30`
- Total order value: `63.99 + 45.30 = 109.29`

---

### 3.3 Payments Dataset

**File:** `olist_order_payments_dataset.csv`

**Purpose:** Records customer payment methods. An order may be paid in a single payment or multiple installments.

**Key Columns:**

| Column | Description |
| :--- | :--- |
| `order_id` | Links to Orders dataset. |
| `payment_sequential` | Installment number (1, 2, 3, etc.). |
| `payment_type` | Payment method (credit_card, boleto, voucher, debit_card). |
| `payment_value` | Value of this installment. |

**Relationship:** One order can have multiple payment rows (one per installment).

---

### 3.4 Reviews Dataset

**File:** `olist_order_reviews_dataset.csv`

**Purpose:** Customer satisfaction surveys. After delivery (or when estimated delivery date is due), customers are asked to rate their experience.

**Key Columns:**

| Column | Description |
| :--- | :--- |
| `order_id` | Links to Orders dataset. |
| `review_score` | Rating from 1 to 5. |
| `review_comment_message` | Free-text customer comment. |

**Note:** One review per order. Reviews are linked to `order_id`, not to individual products.

---

### 3.5 Customers Dataset

**File:** `olist_customers_dataset.csv`

**Purpose:** Static customer data, including location.

**Key Columns:**

| Column | Description |
| :--- | :--- |
| `customer_id` | Unique ID for this order. **Warning:** This is not unique per customer. |
| `customer_unique_id` | **True unique customer identifier.** Same customer across multiple orders has the same `customer_unique_id`. |
| `customer_zip_code_prefix` | Zip code prefix. Links to Geolocation dataset. |

**Important:** For counting unique customers, always use `customer_unique_id`, not `customer_id`.

---

### 3.6 Sellers Dataset

**File:** `olist_sellers_dataset.csv`

**Purpose:** Static seller data, including location.

**Key Columns:**

| Column | Description |
| :--- | :--- |
| `seller_id` | Unique seller identifier. Links to Order Items. |
| `seller_zip_code_prefix` | Zip code prefix. Links to Geolocation dataset. |

---

### 3.7 Products Dataset

**File:** `olist_products_dataset.csv`

**Purpose:** Product catalog and physical attributes used for freight calculation.

**Key Columns:**

| Column | Description |
| :--- | :--- |
| `product_id` | Unique product identifier. |
| `product_category_name` | Category name in Portuguese. |
| `product_weight_g` | Product weight in grams. |
| `product_length_cm` | Product length. |
| `product_height_cm` | Product height. |
| `product_width_cm` | Product width. |

**Note:** Category names require translation using the Category Name Translation file.

---

### 3.8 Geolocation Dataset

**File:** `olist_geolocation_dataset.csv`

**Purpose:** Lookup table mapping zip codes to city, state, and geographic coordinates.

**Key Columns:**

| Column | Description |
| :--- | :--- |
| `geolocation_zip_code_prefix` | Zip code prefix. |
| `geolocation_city` | City name. |
| `geolocation_state` | State code. |
| `geolocation_lat` | Latitude. |
| `geolocation_lng` | Longitude. |

**Role:** Lookup table. Used to enrich Customers and Sellers with city/state information.

---

### 3.9 Category Name Translation

**File:** `product_category_name_translation.csv`

**Purpose:** Maps Portuguese category names to English translations.

**Columns:**

| Column | Description |
| :--- | :--- |
| `product_category_name` | Category name in Portuguese. |
| `product_category_name_english` | Category name in English. |

---


## 4. File Handling Rules

| Rule | Description |
| :--- | :--- |
| **Read-Only** | Files are never modified in the Landing layer. |
| **No Renaming** | Column names remain unchanged. |
| **No Filtering** | All rows are kept. |
| **No Transformations** | No data type changes, cleaning, or enrichment. |

---

