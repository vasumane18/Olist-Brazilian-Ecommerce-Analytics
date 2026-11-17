# Olist-Brazilian-Ecommerce-Analytics
# Brazilian E-Commerce Sales & Operations Analytics (Olist)

An end-to-end Data Analytics and Business Intelligence project analyzing 100,000+ real-world e-commerce orders (2016–2018) from the **Brazilian E-Commerce Public Dataset by Olist**. This repository documents the complete workflow from raw relational database querying in **MySQL** to interactive executive reporting in **Power BI**.

---

## Project Architecture & Workflow

1. **Data Storage & Exploration (MySQL Workbench):** Imported raw CSV datasets into a local MySQL schema, established relational keys, cleaned data types, and ran exploratory SQL queries.
2. **Data Integration (Power BI):** Connected Power BI Desktop directly to the local MySQL instance via the MySQL Connector/NET driver using **Import Mode**.
3. **Data Modeling (Star Schema):** Linked lookup tables (`customers`, `products`) and detail tables (`order_items`, `payments`) to the central fact table (`orders`).
4. **Business Logic (DAX):** Built dynamic measures in a dedicated `Key Measures` table for KPI tracking.
5. **Visualization (3-Page Report):** Designed an interactive, cross-filtered dashboard with synchronized slicers across all pages.

---

## Part 1: MySQL Database & SQL Analysis

### Schema & Tables Used
* `olist_orders_dataset` *(Central Fact Table: order timestamps, status, delivery dates)*
* `olist_customers_dataset` *(Customer unique IDs, city, state)*
* `olist_order_items_dataset` *(Line items, product IDs, prices, freight values)*
* `olist_products_dataset` *(Product IDs, category names)*
* `olist_order_payments_dataset` *(Payment types, installments, payment values)*

### Key SQL Queries & Logic Implemented

#### 1. Data Quality & Anomaly Checks
* **Missing Delivery Dates:** Checked for orders marked `'delivered'` where `order_delivered_customer_date` was `NULL` or empty using conditional boolean grouping.
* **Logical Date Errors:** Queried for dirty records where `CAST(order_delivered_customer_date AS DATETIME) < CAST(order_purchase_timestamp AS DATETIME)`.
* **Order Status & Cancellation Share:** Calculated total volume and percentage share per status using a scalar subquery:
  ```sql
  SELECT 
      order_status,
      COUNT(*) AS order_count,
      ROUND(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM olist_orders_dataset), 2) AS percentage_share
  FROM olist_orders_dataset
  GROUP BY order_status
  ORDER BY order_count DESC;
