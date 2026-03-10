# Data Dictionary

This data dictionary summarizes the key tables and fields used in the **Brazil E-Commerce Revenue & Demand Analysis** project.

> Note: Field names below follow the standard Olist dataset schema. Your loaded tables should match these columns unless renamed.

---

## 1) Raw dataset tables (CSV → PostgreSQL)

### 1.1 `olist_customers_dataset`
- **customer_id** *(PK)*: Customer identifier used to link orders.
- customer_unique_id: De-duplicated customer identifier across multiple orders.
- customer_zip_code_prefix: Zip code prefix.
- customer_city
- customer_state

### 1.2 `olist_orders_dataset`
- **order_id** *(PK)*
- **customer_id** *(FK → customers.customer_id)*
- order_status *(e.g., delivered, shipped, canceled)*
- order_purchase_timestamp
- order_approved_at
- order_delivered_carrier_date
- order_delivered_customer_date
- order_estimated_delivery_date

### 1.3 `olist_order_items_dataset`
- **order_id** *(FK → orders.order_id)*
- **order_item_id** *(Line item identifier within order)*
- **product_id** *(FK → products.product_id)*
- **seller_id** *(FK → sellers.seller_id)*
- shipping_limit_date
- price
- freight_value

### 1.4 `olist_order_payments_dataset`
- **order_id** *(FK → orders.order_id)*
- payment_sequential
- payment_type
- payment_installments
- payment_value

### 1.5 `olist_order_reviews_dataset`
- **review_id** *(PK)*
- **order_id** *(FK → orders.order_id)*
- review_score
- review_comment_title
- review_comment_message
- review_creation_date
- review_answer_timestamp

### 1.6 `olist_products_dataset`
- **product_id** *(PK)*
- product_category_name *(Portuguese category label)*
- product_name_lenght
- product_description_lenght
- product_photos_qty
- product_weight_g
- product_length_cm
- product_height_cm
- product_width_cm

### 1.7 `olist_sellers_dataset`
- **seller_id** *(PK)*
- seller_zip_code_prefix
- seller_city
- seller_state

### 1.8 `olist_geolocation_dataset`
- geolocation_zip_code_prefix
- geolocation_lat
- geolocation_lng
- geolocation_city
- geolocation_state

### 1.9 `product_category_name_translation`
- product_category_name
- product_category_name_english

---

## 2) Analytics star schema tables

### 2.1 `olist_analytics_dim_customer`
- **customer_id** *(PK)*
- customer_unique_id
- customer_city
- customer_state
- customer_zip_code_prefix

### 2.2 `olist_analytics_dim_seller`
- **seller_id** *(PK)*
- seller_city
- seller_state
- seller_zip_code_prefix

### 2.3 `olist_analytics_dim_product`
- **product_id** *(PK)*
- product_category_name
- product_category_name_english
- Optional product attributes (weight/dimensions) if kept

### 2.4 `olist_analytics_dim_date`
- **order_date** *(PK; DATE)*
- year
- month
- month_name
- Optional: quarter, day, weekday

### 2.5 `olist_analytics_fact_sales`
**Grain (recommended):** *one row per order item (order_id + order_item_id)*, filtered to delivered orders.

- **order_id**
- **order_item_id**
- **customer_id** *(FK → dim_customer)*
- **seller_id** *(FK → dim_seller)*
- **product_id** *(FK → dim_product)*
- **order_date** *(FK → dim_date)*
- price
- freight_value
- total_revenue *(= price + freight_value)*

---

## 3) Key definitions

- **Delivered Orders:** Orders where `order_status = 'delivered'`.
- **Item revenue:** `price + freight_value`.
- **Order revenue:** Sum of item revenue across all items in an order.

