# 🐘 PostgreSQL Commands Revision

<div align="center">

A comprehensive PostgreSQL learning repository covering fundamental to intermediate SQL concepts with practical examples using real-world datasets.

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-CC2927?style=for-the-badge&logo=microsoft-sql-server&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)

**[Day 1](#-day-1---sql-fundamentals)** • **[Day 2](#-day-2---sorting-pagination--grouping)** • **[Day 3](#-day-3---advanced-group-by--having)**

</div>

---

## 📚 Table of Contents

- [Overview](#-overview)
- [Learning Progress](#-learning-progress)
- [Day 1 - SQL Fundamentals](#-day-1---sql-fundamentals)
- [Day 2 - Sorting, Pagination & Grouping](#-day-2---sorting-pagination--grouping)
- [Day 3 - Advanced GROUP BY & HAVING](#-day-3---advanced-group-by--having)
- [Database Schemas](#-database-schemas)
- [SQL Execution Order](#-sql-execution-order)
- [Quick Reference](#-quick-reference)
- [Getting Started](#-getting-started)

---

## 🎯 Overview

This repository documents my journey learning PostgreSQL, organized by daily progress. Each day covers specific SQL concepts with hands-on examples using real-world datasets.

### 📊 Datasets Used

| Dataset | Description | Records |
|---------|-------------|---------|
| **`users`** | User management table with authentication data | 10+ users |
| **`models`** | Smartphone specifications (OnePlus, Samsung, Apple, etc.) | 20 phones |
| **`ipl_data`** | IPL cricket ball-by-ball match data | 30 deliveries |

### 🏷️ Brands in Dataset
`OnePlus` • `Samsung` • `Apple` • `Xiaomi` • `Realme` • `Oppo` • `Vivo` • `Nothing` • `Motorola`

---

## 📈 Learning Progress

```
Day 1 ████████████████████ 100% - CRUD Operations & Basic Queries
Day 2 ████████████████████ 100% - Sorting, Pagination & Basic Grouping  
Day 3 ████████████████████ 100% - Advanced GROUP BY & HAVING
```

| Day | Topics | Status |
|-----|--------|--------|
| 1 | CREATE, INSERT, SELECT, UPDATE, DELETE, WHERE, Operators | ✅ Complete |
| 2 | ORDER BY, LIMIT, OFFSET, GROUP BY, Aggregate Functions | ✅ Complete |
| 3 | Multi-column GROUP BY, HAVING, IPL Analytics | ✅ Complete |

---

## 📅 Day 1 - SQL Fundamentals

> **Topics:** DDL, DML, CRUD Operations, WHERE Clause, Operators

### 🔹 1.1 Table Creation (DDL)

```sql
-- Creating table with auto-increment primary key
CREATE TABLE users (
    user_id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL
);
```

**Key Concepts:**
| Keyword | Purpose |
|---------|---------|
| `GENERATED ALWAYS AS IDENTITY` | Auto-increment (PostgreSQL modern way) |
| `PRIMARY KEY` | Unique identifier + auto index |
| `NOT NULL` | Mandatory field |
| `UNIQUE` | No duplicate values allowed |

---

### 🔹 1.2 CRUD Operations

| Operation | Command | Description |
|-----------|---------|-------------|
| **C**reate | `INSERT INTO` | Add new records |
| **R**ead | `SELECT` | Query/retrieve data |
| **U**pdate | `UPDATE ... SET` | Modify existing records |
| **D**elete | `DELETE FROM` | Remove records |

---

### 🔹 1.3 INSERT Variations

```sql
-- ✅ Single Insert with RETURNING (get auto-generated ID)
INSERT INTO users (name, email, password)
VALUES ('NITISH', 'nitish@gmail.com', '1234')
RETURNING user_id;

-- ✅ Bulk Insert (efficient for multiple records)
INSERT INTO users (name, email, password)
VALUES
    ('Amit Sharma', 'amit.sharma@gmail.com', 'pass@123'),
    ('Priya Verma', 'priya.verma@gmail.com', 'priya@2024'),
    ('Rohit Kumar', 'rohit.kumar@gmail.com', 'rk@12345');

-- ✅ UPSERT (Insert or Update on conflict)
INSERT INTO users (name, email, password)
VALUES ('Priya Verma', 'priya.verma@gmail.com', 'new@2025')
ON CONFLICT (email)
DO UPDATE SET
    name = EXCLUDED.name,
    password = EXCLUDED.password;
```

> 💡 **EXCLUDED** refers to the values that were proposed for insertion

---

### 🔹 1.4 SELECT Operations

```sql
-- Basic select all
SELECT * FROM models;

-- Select specific columns
SELECT model, price, rating FROM models;

-- Column aliasing
SELECT model AS "Mobile Name", processor_brand AS "CPU" FROM models;

-- Calculated columns (PPI Formula)
SELECT model, 
    SQRT(resolution_width*resolution_width + resolution_height*resolution_height)/screen_size AS "PPI"  
FROM models;

-- DISTINCT (unique values)
SELECT DISTINCT(brand_name) FROM models;

-- Array aggregation
SELECT array_agg(DISTINCT brand_name) AS "All Brands" FROM models;
```

---

### 🔹 1.5 WHERE Clause & Operators

```sql
-- AND (both conditions must be true)
SELECT * FROM models WHERE brand_name = 'samsung' AND internal_memory > 128;

-- OR (at least one condition true)
SELECT * FROM models WHERE brand_name = 'samsung' OR internal_memory > 128;

-- BETWEEN (inclusive range)
SELECT * FROM models WHERE price BETWEEN 10000 AND 30000;

-- IN (match any value in list)
SELECT * FROM models WHERE processor_brand IN ('snapdragon', 'exynos', 'bionic');

-- NOT IN (exclude values)
SELECT * FROM models WHERE processor_brand NOT IN ('snapdragon', 'exynos');
```

---

### 🔹 1.6 UPDATE & DELETE

```sql
-- ⚠️ Always use WHERE to avoid updating/deleting all rows!

-- Update specific records
UPDATE models SET processor_brand = 'dimensity' WHERE processor_brand = 'helio';

-- Delete with condition
DELETE FROM models WHERE price > 60000;
```

---

## 📅 Day 2 - Sorting, Pagination & Grouping

> **Topics:** ORDER BY, LIMIT, OFFSET, GROUP BY, Aggregate Functions

### 🔹 2.1 ORDER BY (Sorting)

```sql
-- Single column sort (DESC = highest first)
SELECT brand_name, model, price 
FROM models
ORDER BY price DESC;

-- Multiple column sort
SELECT brand_name, model, price, rating
FROM models
ORDER BY brand_name DESC, price ASC;

-- Sort by calculated column
SELECT model, 
    ROUND(SQRT(resolution_width*resolution_width + resolution_height*resolution_height)/screen_size) AS "ppi"  
FROM models 
ORDER BY ppi DESC;
```

| Direction | Order | Example |
|-----------|-------|---------|
| `ASC` | Ascending (default) | A→Z, 1→9 |
| `DESC` | Descending | Z→A, 9→1 |

---

### 🔹 2.2 LIMIT & OFFSET (Pagination)

```sql
-- Top 5 results
SELECT * FROM models ORDER BY price DESC LIMIT 5;

-- Pagination: Skip 5, get next 7 (rows 6-12)
SELECT model, price FROM models 
ORDER BY price DESC 
LIMIT 7 OFFSET 5;
```

**Pagination Formula:**
```
Page 1: LIMIT 10 OFFSET 0   (rows 1-10)
Page 2: LIMIT 10 OFFSET 10  (rows 11-20)
Page N: LIMIT 10 OFFSET (N-1)*10
```

---

### 🔹 2.3 GROUP BY (Aggregation)

```sql
-- Count phones per brand
SELECT brand_name, COUNT(*) AS total_models
FROM models
GROUP BY brand_name
ORDER BY total_models DESC;

-- Multiple aggregations
SELECT 
    brand_name,
    COUNT(*) AS total_models,
    ROUND(AVG(price)) AS avg_price,
    MAX(rating) AS best_rating
FROM models
GROUP BY brand_name;
```

---

### 🔹 2.4 Practical Example: PPI Calculation

```sql
-- PPI = Pixels Per Inch (display sharpness)
-- Formula: √(width² + height²) / screen_size

SELECT model, 
    ROUND(SQRT(resolution_width*resolution_width + resolution_height*resolution_height)/screen_size) AS ppi
FROM models 
ORDER BY ppi DESC
LIMIT 5;
```

---

## 📅 Day 3 - Advanced GROUP BY & HAVING

> **Topics:** Multi-column GROUP BY, HAVING clause, Complex Aggregations, IPL Analytics

### 🔹 3.1 Multi-Column GROUP BY

```sql
-- Group by multiple columns for detailed analysis
SELECT 
    brand_name,
    processor_brand,
    COUNT(*) AS num_phones,
    ROUND(AVG(primary_camera_rear)) AS avg_camera,
    ROUND(AVG(rating)) AS avg_rating
FROM models
GROUP BY brand_name, processor_brand;
```

> Creates groups for each **unique combination** of brand + processor

---

### 🔹 3.2 WHERE vs HAVING

| Clause | When it Filters | Use Case |
|--------|-----------------|----------|
| `WHERE` | **Before** GROUP BY | Filter individual rows |
| `HAVING` | **After** GROUP BY | Filter based on aggregate values |

```sql
-- WHERE: Filter rows BEFORE grouping
SELECT brand_name, COUNT(*) AS count
FROM models
WHERE has_nfc = 'true'
GROUP BY brand_name;

-- HAVING: Filter groups AFTER aggregation
SELECT brand_name, COUNT(*) AS count, AVG(rating) AS avg_rating
FROM models
GROUP BY brand_name
HAVING COUNT(*) > 1
ORDER BY avg_rating DESC;
```

---

### 🔹 3.3 Complex HAVING Conditions

```sql
-- Multiple conditions in HAVING
SELECT brand_name, AVG(price) AS avg_price
FROM models
WHERE has_5g = 'true'
GROUP BY brand_name
HAVING AVG(rating) > 70 AND COUNT(*) > 1;

-- Combined WHERE + HAVING
SELECT 
    brand_name,
    ROUND(AVG(ram_capacity)) AS avg_ram
FROM models
WHERE refresh_rate > 90 AND fast_charging > 60
GROUP BY brand_name
HAVING COUNT(*) > 1
ORDER BY avg_ram DESC;
```

---

### 🔹 3.4 IPL Cricket Analytics

```sql
-- Total runs by each batter
SELECT batter, SUM(batsman_run) AS total_runs
FROM ipl_data
GROUP BY batter
ORDER BY total_runs DESC;

-- Count sixes by each batter
SELECT batter, COUNT(*) AS num_sixes
FROM ipl_data
WHERE batsman_run = 6
GROUP BY batter
ORDER BY num_sixes DESC;

-- Total runs from sixes only
SELECT batter, SUM(batsman_run) AS runs_from_sixes
FROM ipl_data
WHERE batsman_run = 6
GROUP BY batter
ORDER BY runs_from_sixes DESC;
```

---

## 🗄️ Database Schemas

### Users Table
```sql
CREATE TABLE users (
    user_id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL
);
```

### Models Table (Smartphone Data)
```sql
CREATE TABLE models (
    id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    -- Basic Info
    brand_name TEXT, model TEXT, price INTEGER, rating INTEGER,
    -- Connectivity
    has_5g BOOLEAN, has_nfc BOOLEAN, has_ir_blaster BOOLEAN,
    -- Processor
    processor_brand TEXT, num_cores INTEGER, processor_speed NUMERIC,
    -- Battery & Charging
    battery_capacity INTEGER, fast_charging_available BOOLEAN, fast_charging INTEGER,
    -- Memory
    ram_capacity INTEGER, internal_memory INTEGER,
    -- Display
    screen_size NUMERIC, refresh_rate INTEGER,
    resolution_width INTEGER, resolution_height INTEGER,
    -- Camera
    num_rear_cameras INTEGER, num_front_cameras INTEGER,
    primary_camera_rear INTEGER, primary_camera_front INTEGER,
    -- Other
    os TEXT, extended_memory_available BOOLEAN, extended_upto INTEGER
);
```

### IPL Data Table
```sql
CREATE TABLE ipl_data (
    id BIGINT, innings INTEGER, overs INTEGER, ballnumber INTEGER,
    batter TEXT, bowler TEXT, non_striker TEXT,
    extra_type TEXT, batsman_run INTEGER, extras_run INTEGER, total_run INTEGER,
    non_boundary BOOLEAN, iswicketdelivery BOOLEAN,
    player_out TEXT, kind TEXT, fielders_involved TEXT, battingteam TEXT
);
```

---

## � SQL Execution Order

Understanding the order in which SQL processes clauses:

```
┌─────────────────────────────────────────────────┐
│  1. FROM        → Select the table(s)           │
│  2. WHERE       → Filter rows                   │
│  3. GROUP BY    → Group remaining rows          │
│  4. HAVING      → Filter groups                 │
│  5. SELECT      → Choose columns to display     │
│  6. ORDER BY    → Sort the results              │
│  7. LIMIT/OFFSET→ Restrict output rows          │
└─────────────────────────────────────────────────┘
```

---

## �📋 Quick Reference

### SQL Keywords by Day

| Keyword | Purpose | Day |
|---------|---------|:---:|
| `CREATE TABLE` | Define new table structure | 1 |
| `INSERT INTO` | Add new records | 1 |
| `SELECT` | Query data | 1,2,3 |
| `WHERE` | Filter rows (before grouping) | 1,2,3 |
| `UPDATE` | Modify existing records | 1 |
| `DELETE` | Remove records | 1 |
| `DISTINCT` | Get unique values | 1 |
| `AS` | Column/table aliasing | 1,2,3 |
| `AND` / `OR` | Logical operators | 1,2,3 |
| `IN` / `NOT IN` | Set membership | 1,2 |
| `BETWEEN` | Range filter (inclusive) | 1 |
| `ORDER BY` | Sort results | 2,3 |
| `ASC` / `DESC` | Sort direction | 2,3 |
| `LIMIT` | Restrict row count | 2,3 |
| `OFFSET` | Skip rows (pagination) | 2,3 |
| `GROUP BY` | Aggregate grouping | 2,3 |
| `HAVING` | Filter groups (after aggregation) | 3 |
| `ON CONFLICT` | Handle upsert | 1 |
| `RETURNING` | Return inserted data | 1 |

### Aggregate Functions

| Function | Purpose | Example |
|----------|---------|---------|
| `COUNT(*)` | Count rows | `COUNT(*) AS total` |
| `SUM()` | Sum values | `SUM(price) AS total_price` |
| `AVG()` | Average | `AVG(rating) AS avg_rating` |
| `MIN()` | Minimum value | `MIN(price) AS cheapest` |
| `MAX()` | Maximum value | `MAX(price) AS expensive` |
| `ROUND()` | Round numbers | `ROUND(AVG(price))` |
| `SQRT()` | Square root | `SQRT(value)` |

---

## 🚀 Getting Started

### Prerequisites
- PostgreSQL 12+ installed
- psql CLI or any PostgreSQL client (pgAdmin, DBeaver, TablePlus)

### Setup

1️⃣ **Clone the repository:**
```bash
git clone https://github.com/DebjitDebjit2004/PostgreSQL---Commands---Revision.git
cd PostgreSQL---Commands---Revision
```

2️⃣ **Connect to PostgreSQL:**
```bash
psql -U your_username -d your_database
```

3️⃣ **Run SQL files in order:**
```bash
\i 'Day - 1'
\i 'Day - 2'
\i 'Day - 3'
```

---

## 📁 Repository Structure

```
PostgreSQL-Database/
│
├── 📄 Day - 1          # CRUD, WHERE, Operators
├── 📄 Day - 2          # ORDER BY, LIMIT, OFFSET, GROUP BY
├── 📄 Day - 3          # HAVING, Multi-column GROUP BY, IPL Analytics
│
├── 📁 Tables/          # CSV data files
│   ├── users1.csv
│   ├── users2.csv
│   ├── students.csv
│   ├── orders.csv
│   └── ...
│
└── 📄 README.md        # This documentation
```

---

## 🤝 Contributing

Feel free to fork this repository and add your own SQL learning notes!

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/day-4`)
3. Commit your changes (`git commit -m 'Add Day 4 content'`)
4. Push to the branch (`git push origin feature/day-4`)
5. Open a Pull Request

---

## 📝 License

This project is open source and available under the [MIT License](LICENSE).

---

## 👤 Author

<div align="center">

**Debjit Das**

[![GitHub](https://img.shields.io/badge/GitHub-DebjitDebjit2004-181717?style=for-the-badge&logo=github)](https://github.com/DebjitDebjit2004)

</div>

---

<div align="center">

### ⭐ Star this repository if you find it helpful!

**Happy Learning! 🚀**

</div>
