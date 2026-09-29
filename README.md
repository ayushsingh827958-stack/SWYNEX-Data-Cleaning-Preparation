# SWYNEX-Data-Cleaning-Preparation
data cleaning and preparation with retail sales data
# E-Commerce & Retail Sales Data Cleaning

## Overview

This project demonstrates a practical **data cleaning, investigation, and transformation workflow for online retail and e-commerce transaction data**.

The goal is to turn messy transactional data into a reliable dataset that can be used for:

* Sales analysis
* Revenue analysis
* Product performance
* Customer analysis
* Order analysis
* Cancellation/return analysis
* Country/market analysis
* Inventory-related analysis
* E-commerce KPI reporting
* Business dashboards
* Data warehouse development
* Automated reporting

The workflow is designed around problems commonly encountered by:

* Online retailers
* E-commerce businesses
* Dropshipping businesses
* D2C brands
* Marketplace sellers
* Small and medium-sized retailers
* Online product stores

---

# Business Problem

Raw e-commerce transaction data is rarely ready for analysis.

A typical sales dataset may contain:

* Missing product information
* Missing customer information
* Missing transaction dates
* Cancelled orders
* Negative quantities
* Invalid prices
* Duplicate transactions
* Repeated invoice/order numbers
* Different products within the same order
* Multiple transactions for the same customer
* Inconsistent or incomplete records

Simply loading this data into a dashboard can produce misleading business results.

Therefore, this project follows a structured process:

```text
RAW E-COMMERCE DATA
        ↓
DATA PROFILING
        ↓
DATA INVESTIGATION
        ↓
DATA CLEANING
        ↓
BUSINESS TRANSFORMATION
        ↓
DATA VALIDATION
        ↓
ANALYTICS-READY DATA
        ↓
DATABASE / DATA WAREHOUSE
        ↓
BUSINESS REPORTING
```

---

# 1. Dataset

The combined retail dataset contains:

```text
Rows:       1,067,371
Columns:    9
```

### Original Columns

| Column      | Type     | Business Meaning          |
| ----------- | -------- | ------------------------- |
| Invoice     | String   | Order/invoice identifier  |
| StockCode   | String   | Product identifier        |
| Description | String   | Product description       |
| Quantity    | Integer  | Quantity purchased        |
| InvoiceDate | Datetime | Transaction date and time |
| Price       | Float    | Product unit price        |
| Customer ID | Float    | Customer identifier       |
| Country     | String   | Customer market/country   |
| DatasetYear | String   | Dataset/source period     |

The dataset represents **transaction-line level sales data**.

For example, one order can contain multiple products:

```text
Order 10001
│
├── Product A → Quantity 2
├── Product B → Quantity 1
├── Product C → Quantity 5
└── Product D → Quantity 3
```

Therefore, repeated order numbers are expected and are not automatically treated as duplicate orders.

---

# 2. Data Profiling

Before modifying the dataset, a complete data profile was created.

The profiling stage examines:

```text
Data Structure
Missing Data
Unique Values
Duplicate Data
Numeric Data
Categorical Data
Date Data
```

The purpose is to understand **what problems actually exist before deciding how to fix them**.

---

# 3. Data Quality Findings

## Missing Data

| Field       | Missing Records | Missing % |
| ----------- | --------------: | --------: |
| Description |           4,382 |     0.41% |
| InvoiceDate |         616,193 |    57.73% |
| Customer ID |         243,007 |    22.77% |

### Business Impact

### Missing Description

A missing product description can affect:

* Product reporting
* Product catalog analysis
* Product-level dashboards

However, the product may still be identifiable through `StockCode`.

---

### Missing InvoiceDate

A missing transaction date is particularly important for e-commerce analytics because it affects:

* Daily sales
* Monthly sales
* Seasonal analysis
* Sales trends
* Customer purchase timing
* Revenue by period

Therefore, missing dates are investigated rather than automatically replaced.

---

### Missing Customer ID

Missing customer identifiers affect:

* Customer lifetime analysis
* Customer segmentation
* Repeat-purchase analysis
* Customer-level revenue
* Customer retention analysis

The transaction may still be useful for overall sales analysis even when customer identity is unavailable.

---

# 4. Order / Invoice Investigation

The dataset contains:

```text
Unique invoices:
53,628
```

Repeated invoice numbers are expected because an e-commerce order can contain multiple products.

Example:

```text
Invoice 489434

Product A → 12 units
Product B → 12 units
Product C → 48 units
Product D → 24 units
```

Therefore:

```text
Repeated Invoice
       ≠
Duplicate Transaction
```

This distinction is important when cleaning e-commerce data.

Removing all repeated invoices would incorrectly delete legitimate order-line records.

---

# 5. Duplicate Transaction Investigation

Complete duplicate rows were investigated separately from repeated column values.

Initial dataset:

```text
1,067,371 rows
```

Duplicate transaction rows removed:

```text
12,135
```

After cleaning:

```text
1,055,231 rows
```

Final duplicate-row check:

```text
Remaining duplicate rows: 0
```

This ensures that identical transaction records are removed without deleting legitimate multi-product orders.

---

# 6. Quantity Investigation

Quantity statistics:

| Metric              |   Value |
| ------------------- | ------: |
| Minimum             | -80,995 |
| Maximum             |  80,995 |
| Mean                |    9.94 |
| Median              |       3 |
| Negative quantities |  22,950 |
| Zero quantities     |       0 |

Negative quantities were not treated simply as statistical outliers.

In an e-commerce environment, negative quantities can represent transactions such as:

* Cancellations
* Returns
* Reversals
* Order adjustments

Therefore, the data was transformed to preserve this business meaning.

---

# 7. Sales vs Cancellation Classification

A new business field was created:

```text
Transformation
```

Transactions were classified as:

```text
Quantity > 0
       ↓
Sale

Quantity < 0
       ↓
Cancellation
```

### Before subsequent cleaning

| Transaction Type |   Records |
| ---------------- | --------: |
| Sale             | 1,044,421 |
| Cancellation     |    22,950 |

### After cleaning

| Transaction Type |   Records |
| ---------------- | --------: |
| Sale             | 1,032,342 |
| Cancellation     |    22,889 |

This allows the business to distinguish between:

```text
Gross transaction activity
        ↓
Sales
        +
Cancellations
```

instead of deleting cancellation records and losing potentially useful business information.

---

# 8. Price Validation

Price profiling identified:

```text
Minimum price: -53,594.36
Maximum price: 38,970.00
Negative prices: 5
Zero prices: 6,202
```

Negative prices were investigated as invalid price records.

A validation field was created:

```text
IsValidPrice
```

Initial validation:

| IsValidPrice |   Records |
| ------------ | --------: |
| 1            | 1,061,164 |
| 0            |     6,207 |

Negative-price records removed:

```text
5
```

The validation field makes it possible to distinguish between:

```text
Valid Price
Invalid Price
```

rather than hiding the data-quality decision.

---

# 9. Zero Price Investigation

Zero-price transactions were not automatically treated as the same problem as negative prices.

A zero price can have different business meanings depending on the e-commerce business, for example:

* Promotional products
* Free products
* Samples
* Gifts
* Adjustments
* Data-entry issues

Therefore, zero-price records are retained for further business investigation instead of being blindly deleted.

---

# 10. Product Investigation

Products were analyzed using:

```text
StockCode
Description
Price
Quantity
```

The dataset contains:

```text
Unique StockCodes: 5,305
Unique Descriptions: 5,698
```

This allows later investigation of:

* Best-selling products
* Low-selling products
* Product revenue
* Product cancellations
* Product pricing
* Product demand
* Product catalog quality

---

# 11. Customer Data Investigation

The dataset contains:

```text
Unique Customer IDs:
5,942
```

Customer IDs are useful for:

* Customer revenue
* Repeat purchases
* Order frequency
* Customer segmentation
* Customer lifetime value
* Customer retention analysis

However, because some transactions do not contain a customer ID, customer-level analysis must account for incomplete customer identification.

---

# 12. Geographic Data

The dataset contains:

```text
43 countries
```

Country information can later support:

* Revenue by country
* Orders by country
* Product demand by market
* Cancellation rate by market
* Market expansion analysis
* International e-commerce analysis

---

# 13. Date Investigation

`InvoiceDate` was converted into a datetime field.

Date range:

```text
Minimum:
2009-01-12 07:45:00

Maximum:
2011-12-10 17:19:00
```

The date field was investigated by:

* Year
* Month
* Day of week
* Hour

This prepares the dataset for business questions such as:

```text
Which month generates the most sales?

Which days have the highest order activity?

What are the busiest selling hours?

How does sales performance change over time?

Are cancellations concentrated in particular periods?
```

---

# 14. Data Cleaning Results

### Initial Dataset

```text
1,067,371 rows
9 columns
```

### Cleaning Operations

The dataset went through:

```text
1. Missing-value investigation
2. Duplicate investigation
3. Complete duplicate-row removal
4. Quantity investigation
5. Sale/cancellation classification
6. Price validation
7. Invalid negative-price removal
8. Post-cleaning validation
```

### Final Dataset

```text
Rows:
1,055,231

Columns:
11

Duplicate rows:
0
```

Two additional business/quality fields were created:

```text
Transformation
IsValidPrice
```

---

# 15. Final Data Quality Status

| Field          | Final Missing Records |
| -------------- | --------------------: |
| Invoice        |                     0 |
| StockCode      |                     0 |
| Description    |                 4,382 |
| Quantity       |                     0 |
| InvoiceDate    |               609,135 |
| Price          |                     0 |
| Customer ID    |               242,865 |
| Country        |                     0 |
| DatasetYear    |                     0 |
| Transformation |                     0 |
| IsValidPrice   |                     0 |

The remaining missing values are preserved where a reliable business rule has not yet justified replacement.

This is intentional.

```text
Missing value
      ↓
Investigate
      ↓
Understand business meaning
      ↓
Decide
```

rather than:

```text
Missing value
      ↓
Automatically fill/delete
```

---

# 16. Business-Ready Transformation

The cleaned dataset now contains both raw transaction information and derived business information.

Example:

| Quantity | Price | Transformation | IsValidPrice |
| -------: | ----: | -------------- | -----------: |
|       12 |  6.95 | Sale           |            1 |
|       12 |  6.75 | Sale           |            1 |
|       12 |  6.75 | Sale           |            1 |
|       48 |  2.10 | Sale           |            1 |
|       24 |  1.25 | Sale           |            1 |

This creates a foundation for calculating business metrics such as:

```text
Revenue
Orders
Units Sold
Average Order Value
Average Selling Price
Cancellation Rate
Product Revenue
Customer Revenue
Country Revenue
Monthly Sales
```

---

# 17. Why This Matters for E-Commerce Businesses

Clean transaction data is the foundation of reliable business decisions.

For an online retailer, incorrect data can lead to incorrect answers to questions such as:

```text
Which products generate the most revenue?

Which customers generate the most sales?

Which countries are the largest markets?

How many orders are cancelled?

What products have unusual pricing?

What months generate the most revenue?

How many units are actually sold?

Which products should receive more attention?
```

If the underlying transaction data is incorrect, the resulting dashboard or report can also be incorrect.

Therefore:

```text
Reliable Analytics
        ↓
Reliable Data
        ↓
Reliable Cleaning
        ↓
Correct Understanding of Raw Data
```

---

# 18. Target Business Use Cases

This workflow can be adapted for:

### E-Commerce Stores

```text
Orders
Products
Customers
Revenue
Returns
Countries
Sales Trends
```

### Dropshipping Businesses

```text
Product performance
Order volume
Cancellation activity
Pricing validation
Market performance
Customer activity
```

### D2C Brands

```text
Customer revenue
Product performance
Repeat purchases
Sales trends
Market analysis
```

### Marketplace Sellers

```text
Order analysis
Product sales
Cancellation analysis
Customer activity
Geographic performance
```

### Small & Medium Retailers

```text
Sales reporting
Product analysis
Customer analysis
Monthly performance
Business dashboards
```

---

# 19. End-to-End Project Architecture

The data-cleaning stage is one part of the larger retail analytics pipeline:

```text
                    RAW DATA
                       │
                       ▼
              ┌─────────────────┐
              │ DATA PROFILING   │
              └─────────────────┘
                       │
                       ▼
              ┌─────────────────┐
              │  INVESTIGATION  │
              └─────────────────┘
                       │
                       ▼
              ┌─────────────────┐
              │ DATA CLEANING   │
              └─────────────────┘
                       │
                       ▼
              ┌─────────────────┐
              │ TRANSFORMATION  │
              └─────────────────┘
                       │
                       ▼
              ┌─────────────────┐
              │   VALIDATION    │
              └─────────────────┘
                       │
                       ▼
              ┌─────────────────┐
              │     SQL DB      │
              └─────────────────┘
                       │
                       ▼
              ┌─────────────────┐
              │ DATA WAREHOUSE  │
              └─────────────────┘
                       │
                       ▼
              ┌─────────────────┐
              │   ANALYTICS     │
              └─────────────────┘
                       │
                       ▼
              ┌─────────────────┐
              │ BI / REPORTING  │
              └─────────────────┘
```

---

# 20. Core Principle

This project does not treat data cleaning as simply deleting bad rows.

The workflow is:

```text
PROFILE
   ↓
FIND THE PROBLEM
   ↓
INVESTIGATE THE PROBLEM
   ↓
UNDERSTAND BUSINESS MEANING
   ↓
DEFINE A RULE
   ↓
TRANSFORM / CLEAN
   ↓
VALIDATE
   ↓
DOCUMENT
```

The objective is to produce data that is not only technically clean, but also **meaningful for e-commerce business analysis**.

---

# Project Status

### Completed

* [x] Data profiling
* [x] Missing-value investigation
* [x] Unique-value analysis
* [x] Duplicate investigation
* [x] Numeric profiling
* [x] Categorical profiling
* [x] Date profiling
* [x] Transaction classification
* [x] Price validation
* [x] Duplicate-row removal
* [x] Invalid negative-price removal
* [x] Final validation

### Next Stage

* [ ] Business-focused transformations
* [ ] Revenue calculations
* [ ] Date dimension fields
* [ ] Product/customer/order modeling
* [ ] SQL database loading
* [ ] Data warehouse design
* [ ] Sales analysis
* [ ] E-commerce KPI analysis
* [ ] BI dashboard
* [ ] Business insights

---

## Final Objective

Build a complete retail/e-commerce data pipeline that takes **raw transaction data** and turns it into a reliable system for **sales analytics, business intelligence, and decision support**.

The project is designed to demonstrate the complete journey:

```text
Raw E-Commerce Data
        ↓
Understand
        ↓
Investigate
        ↓
Clean
        ↓
Transform
        ↓
Validate
        ↓
Store
        ↓
Analyze
        ↓
Report
```
