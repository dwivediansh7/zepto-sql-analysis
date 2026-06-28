# Zepto SQL Data Analysis Project

A real-world SQL portfolio project built on e-commerce inventory data scraped from Zepto, one of India's fastest-growing quick-commerce platforms. This project replicates how data analysts actually work: messy raw data, cleaning, EDA, and business insights.

---

## Project Overview

The goal is to simulate the complete data analyst workflow on Zepto's product catalog data:

* Set up a real-world e-commerce inventory database from raw CSV data
* Explore the data — categories, pricing structures, stock patterns
* Clean the data — nulls, zero-price entries, paise-to-rupee conversion
* Derive business insights — discounts, revenue potential, inventory gaps, pricing tiers

---

## Repository Structure

```
zepto-sql-analysis/
│
├── Zepto_SQL_data_analysis.sql   # All queries: setup, EDA, cleaning, insights
├── zepto_v2.csv                  # Raw dataset (Zepto product listings)
└── README.md
```

---

## Dataset Overview

Sourced from Kaggle, originally scraped from Zepto's live product listings. Each row is a unique SKU (Stock Keeping Unit), meaning the same product can appear multiple times across different weights, package sizes, or discount tiers — exactly how real catalog data looks.

| Column | Description |
|---|---|
| `sku_id` | Unique identifier per SKU (Synthetic Primary Key) |
| `name` | Product name as listed on the app |
| `category` | Category (Fruits, Snacks, Beverages, etc.) |
| `mrp` | Maximum Retail Price (originally in paise, converted to rupees) |
| `discountPercent` | Discount applied on MRP |
| `discountedSellingPrice` | Final price after discount (converted to rupees) |
| `availableQuantity` | Units currently in inventory |
| `weightInGms` | Product weight in grams |
| `outOfStock` | Boolean — whether the product is available |
| `quantity` | Units per package (or grams for loose produce) |

---

## Project Workflow

### 1. Database and Table Creation

```sql
CREATE TABLE zepto (
  sku_id SERIAL PRIMARY KEY,
  category VARCHAR(120),
  name VARCHAR(150) NOT NULL,
  mrp NUMERIC(8,2),
  discountPercent NUMERIC(5,2),
  availableQuantity INTEGER,
  discountedSellingPrice NUMERIC(8,2),
  weightInGms INTEGER,
  outOfStock BOOLEAN,
  quantity INTEGER
);
```

### 2. Data Import

Loaded via pgAdmin's import tool. If facing encoding issues, use:

```sql
\copy zepto(category, name, mrp, discountPercent, availableQuantity,
            discountedSellingPrice, weightInGms, outOfStock, quantity)
FROM 'zepto_v2.csv' WITH (FORMAT csv, HEADER true, DELIMITER ',', ENCODING 'UTF8');
```

If you hit UTF-8 errors, re-save the CSV as CSV UTF-8 in Excel before importing.

### 3. Exploratory Data Analysis

* Total record count and dataset structure
* Sample rows to understand schema
* NULL checks across all columns
* Distinct product categories
* In-stock vs out-of-stock product distribution
* Duplicate product names across different SKUs

### 4. Data Cleaning

* Removed rows where mrp or discountedSellingPrice was zero (invalid entries)
* Converted mrp and discountedSellingPrice from paise to rupees (divided by 100)

### 5. Business Insights

| Analysis | Description |
|---|---|
| Best-value products | Top 10 SKUs by discount percentage |
| Out-of-stock premium items | High-MRP products currently unavailable |
| Category revenue potential | Estimated revenue = price x available quantity |
| Low-discount premium products | MRP above 500 with less than 5% discount |
| Top discount categories | Top 5 categories by average discount offered |
| Price per gram | Value-for-money comparison across products |
| Weight segmentation | Products grouped as Low, Medium, and Bulk |
| Inventory weight by category | Total weight in stock per category |

---

## How to Run

```bash
git clone https://github.com/dwivediansh7/zepto-sql-analysis.git
cd zepto-sql-analysis
```

1. Open pgAdmin or any PostgreSQL client
2. Create a new database
3. Run `Zepto_SQL_data_analysis.sql` sequentially from top to bottom
4. Import `zepto_v2.csv` when prompted

Works with PostgreSQL. Minor syntax changes may be needed for MySQL or SQLite.

---

## Key Takeaways

* Practiced real analyst thinking — not just writing queries, but asking why behind the numbers
* Worked with genuinely messy data including encoding issues, paise-to-rupee mismatch, and duplicate SKUs
* Wrote queries that answer actual business questions a product or growth team would care about
