# 📄 Olist Data Engineering Project – Power BI Dashboard

## 1. Dashboard Overview

The dashboard consists of **three pages** designed to give stakeholders a complete view of business performance.

| Page | Name | Purpose |
| :--- | :--- | :--- |
| **Page 1** | Overview | Executive summary with key KPIs and high-level trends |
| **Page 2** | Products & Sellers | Deep dive into product categories and seller performance |
| **Page 3** | Logistics & Satisfaction | Delivery performance and customer reviews |

---

## 2. Page 1: Overview

### 2.1 Purpose

Provides a high-level summary of the business for executives. Answers "How is the business doing?" at a glance.

### 2.2 Key KPIs (Card Visuals)

| KPI | Value |
| :--- | :--- |
| **Total Revenue** | 13.49M BRL |
| **Total Orders** | 98,196 |
| **Avg Order Value** | 137.41 BRL |
| **On-Time Delivery** | 92.2% |
| **Avg Review Score** | 4.03 / 5 |


### 2.3 Key Insights

| Insight | Implication |
| :--- | :--- |
| **71% of revenue from São Paulo** | Geographic concentration risk |
| **health_beauty is top category** | Invest more in this category |
| **Top sellers drive most revenue** | Retain top sellers with incentives |
| **On-time delivery is 92.2%** | Strong logistics performance |
| **Review response rate is 7.8%** | Encourage more customer reviews |

### 2.4 Recommendations

| Priority | Action |
| :--- | :--- |
| 1 | Expand marketing to states outside SP |
| 2 | Invest in health_beauty and watches_gifts |
| 3 | Launch seller incentives for top performers |
| 4 | Run campaigns to encourage customer reviews |
| 5 | Monitor delivery performance |

---

## 3. Page 2: Products & Sellers

### 3.1 Purpose

Provides detailed analysis of product categories and seller performance.

### 3.2 Key KPIs (Card Visuals)

| KPI | Value |
| :--- | :--- |
| **Total Revenue** | 13.49M BRL |
| **Total Orders** | 98K |
| **Avg Order Value** | 137.41 BRL |
| **Total Products Sold** | 33K |


### 3.3 Key Insights

| # | Insight | Implication |
| :--- | :--- | :--- |
| 1 | health_beauty leads both revenue and orders | Strong demand. Invest more. |
| 2 | watches_gifts: high revenue, low orders | Premium pricing works. Protect margins. |
| 3 | bed_bath_table: highest orders, lower revenue | Volume-driven. Consider upselling. |
| 4 | Top sellers dominate revenue | Create loyalty programs to retain them. |
| 5 | cool_stuff and auto are underperforming | Investigate pricing, quality, or demand. |

### 3.4 Recommendations

| Priority | Action |
| :--- | :--- |
| 1 | Invest in health_beauty and watches_gifts |
| 2 | Analyze cool_stuff and auto performance |
| 3 | Create incentives for top sellers |
| 4 | Support low-performing sellers with training |
| 5 | Promote upselling in bed_bath_table |

---

## 4. Page 3: Logistics & Satisfaction

### 4.1 Purpose

Focuses on operational efficiency (delivery) and customer experience (reviews).

### 4.2 Key KPIs (Card Visuals)

| KPI | Value |
| :--- | :--- |
| **Avg Delivery Days** | 12.41 Days |
| **On-Time Delivery Rate** | 92.2% |
| **Avg Review Score** | 4.03 / 5 |
| **Review Response Rate** | 7.82% |


### 4.3 Key Insights

| # | Insight | Implication |
| :--- | :--- | :--- |
| 1 | 92.2% on-time delivery is strong | Maintain logistics performance |
| 2 | Review response rate is very low (7.82%) | Encourage customers to leave reviews |
| 3 | Avg review score is 4.03 – good but can improve | Monitor categories with lower scores |
| 4 | Some states have lower on-time rates | Investigate logistics issues |
| 5 | Review scores improved over time | Quality and service are improving |

### 4.4 Recommendations

| Priority | Action |
| :--- | :--- |
| 1 | Investigate states with low on-time delivery |
| 2 | Launch campaign to encourage reviews |
| 3 | Maintain or improve 92.2% delivery rate |
| 4 | Review products with low scores (1-2 stars) |
| 5 | Monitor freight costs in high-volume states |

---

## 5. Global Slicers (Filters)

All three pages share the same global filters for consistent analysis.

| Slicer | Field | Type |
| :--- | :--- | :--- |
| **Date** | FullDate (DimDate) | Between (range) |
| **State** | CustomerState | Dropdown |
| **Category** | CategoryNameEnglish | Dropdown |

---


## 6. Dashboard Summary

### 6.1 What the Dashboard Answers

| Question | Page |
| :--- | :--- |
| How much revenue are we generating? | Overview |
| Which states are our best markets? | Overview |
| What are our top selling categories? | Products & Sellers |
| Who are our top sellers? | Products & Sellers |
| How fast do we deliver? | Logistics & Satisfaction |
| Are customers satisfied? | Logistics & Satisfaction |

### 6.2 Final Takeaway

Olist is performing well overall, but it's too dependent on one state and a few top sellers. Growth opportunities exist in new regions, underperforming categories, and customer engagement. Prioritize diversification and retention to scale sustainably.

---

**Documentation prepared by:** Saif  
**Project:** Olist Data Engineering Project  
**Phase:** Power BI Dashboard  
**Version:** 1.0
