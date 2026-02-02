# 🐘 PostgreSQL Commands Revision

A comprehensive PostgreSQL learning repository covering fundamental to intermediate SQL concepts with practical examples using real-world datasets.

![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-CC2927?style=for-the-badge&logo=microsoft-sql-server&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)

---

## 📚 Table of Contents

- [Overview](#-overview)
- [Day 1 - SQL Fundamentals](#-day-1---sql-fundamentals)
- [Day 2 - Sorting, Filtering & Grouping](#-day-2---sorting-filtering--grouping)
- [Database Schemas](#-database-schemas)
- [Quick Reference](#-quick-reference)
- [Getting Started](#-getting-started)

---

## 🎯 Overview

This repository documents my journey learning PostgreSQL, organized by daily progress. Each day covers specific SQL concepts with hands-on examples using two main datasets:

| Dataset | Description |
|---------|-------------|
| **`users`** | User management table with authentication data |
| **`models`** | Smartphone specifications dataset (20+ phone models) |
| **`ipl_data`** | IPL cricket ball-by-ball match data |

---

## 📅 Day 1 - SQL Fundamentals

### Topics Covered

#### 🔹 Table Creation
```sql
CREATE TABLE users (
    user_id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL
);
```

#### 🔹 CRUD Operations

| Operation | Command | Description |
|-----------|---------|-------------|
| **Create** | `INSERT INTO` | Add new records |
| **Read** | `SELECT` | Query data |
| **Update** | `UPDATE ... SET` | Modify existing records |
| **Delete** | `DELETE FROM` | Remove records |

#### 🔹 INSERT Variations
```sql
-- Single insert with RETURNING
INSERT INTO users (name, email, password)
VALUES ('NITISH', 'nitish@gmail.com', '1234')
RETURNING user_id;

-- Bulk insert
INSERT INTO users (name, email, password)
VALUES
    ('Amit Sharma', 'amit.sharma@gmail.com', 'pass@123'),
    ('Priya Verma', 'priya.verma@gmail.com', 'priya@2024');

-- Upsert (INSERT ... ON CONFLICT)
INSERT INTO users (name, email, password)
VALUES ('Priya Verma', 'priya.verma@gmail.com', 'new@2025')
ON CONFLICT (email)
DO UPDATE SET
    name = EXCLUDED.name,
    password = EXCLUDED.password;
```

#### 🔹 SELECT Operations
```sql
-- Basic select
SELECT * FROM models WHERE TRUE;

-- Column aliasing
SELECT model AS "mobile name", processor_brand AS "CPU" FROM models;

-- Calculated columns (PPI calculation)
SELECT model, 
    SQRT(resolution_width*resolution_width + resolution_height*resolution_height)/screen_size AS "PPI"  
FROM models;

-- DISTINCT values
SELECT DISTINCT(brand_name) AS "All brands" FROM models;

-- Array aggregation
SELECT array_agg(DISTINCT brand_name) AS "All brands" FROM models;
```

#### 🔹 WHERE Clause & Operators
```sql
-- AND / OR conditions
SELECT * FROM models WHERE brand_name = 'samsung' AND internal_memory > 128;

-- BETWEEN range
SELECT * FROM models WHERE price BETWEEN 10000 AND 30000;

-- IN / NOT IN
SELECT * FROM models WHERE processor_brand IN ('snapdragon', 'exynos', 'bionic');
SELECT * FROM models WHERE processor_brand NOT IN ('snapdragon', 'exynos', 'bionic');
```

#### 🔹 UPDATE & DELETE
```sql
-- Update records
UPDATE models SET processor_brand = 'dimensity' WHERE processor_brand = 'helio';

-- Delete records
DELETE FROM models WHERE price > 60000;
```

---

## 📅 Day 2 - Sorting, Filtering & Grouping

### Topics Covered

#### 🔹 ORDER BY (Sorting)
```sql
-- Single column sort
SELECT BRAND_NAME, MODEL, PRICE, SCREEN_SIZE 
FROM MODELS
ORDER BY SCREEN_SIZE DESC;

-- Multiple column sort
SELECT BRAND_NAME, MODEL, PRICE, RATING
FROM MODELS
ORDER BY BRAND_NAME DESC, PRICE ASC;

-- Sort by calculated column
SELECT model, 
    ROUND(SQRT((resolution_width*resolution_width + resolution_height*resolution_height))/screen_size) AS "ppi"  
FROM models 
ORDER BY ppi DESC;
```

#### 🔹 LIMIT & OFFSET (Pagination)
```sql
-- Top N results
SELECT * FROM MODELS ORDER BY SCREEN_SIZE DESC LIMIT 3;

-- Pagination (skip first 5, get next 7)
SELECT model, ppi FROM models ORDER BY ppi DESC LIMIT 7 OFFSET 5;
```

#### 🔹 GROUP BY (Aggregation)
```sql
-- Count models per brand
SELECT BRAND_NAME, COUNT(*) AS Total_models
FROM MODELS
GROUP BY BRAND_NAME
ORDER BY Total_models DESC;
```

#### 🔹 Calculated Columns
```sql
-- Total cameras calculation
SELECT 
    model, 
    NUM_REAR_CAMERAS, 
    NUM_FRONT_CAMERAS, 
    (NUM_REAR_CAMERAS + NUM_FRONT_CAMERAS) AS "total_cameras"
FROM MODELS
ORDER BY total_cameras DESC LIMIT 10;
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
    brand_name TEXT,
    model TEXT,
    price INTEGER,
    rating INTEGER,
    has_5g BOOLEAN,
    has_nfc BOOLEAN,
    has_ir_blaster BOOLEAN,
    processor_brand TEXT,
    num_cores INTEGER,
    processor_speed NUMERIC,
    battery_capacity INTEGER,
    fast_charging_available BOOLEAN,
    fast_charging INTEGER,
    ram_capacity INTEGER,
    internal_memory INTEGER,
    screen_size NUMERIC,
    refresh_rate INTEGER,
    num_rear_cameras INTEGER,
    num_front_cameras INTEGER,
    os TEXT,
    primary_camera_rear INTEGER,
    primary_camera_front INTEGER,
    extended_memory_available BOOLEAN,
    extended_upto INTEGER,
    resolution_width INTEGER,
    resolution_height INTEGER
);
```

### IPL Data Table
```sql
CREATE TABLE ipl_data (
    id BIGINT,
    innings INTEGER,
    overs INTEGER,
    ballnumber INTEGER,
    batter TEXT,
    bowler TEXT,
    non_striker TEXT,
    extra_type TEXT,
    batsman_run INTEGER,
    extras_run INTEGER,
    total_run INTEGER,
    non_boundary BOOLEAN,
    iswicketdelivery BOOLEAN,
    player_out TEXT,
    kind TEXT,
    fielders_involved TEXT,
    battingteam TEXT
);
```

---

## 📋 Quick Reference

### SQL Keywords Covered

| Keyword | Purpose | Day |
|---------|---------|-----|
| `CREATE TABLE` | Define new table structure | 1 |
| `INSERT INTO` | Add new records | 1 |
| `SELECT` | Query data | 1, 2 |
| `WHERE` | Filter conditions | 1, 2 |
| `UPDATE` | Modify records | 1 |
| `DELETE` | Remove records | 1 |
| `DISTINCT` | Unique values | 1 |
| `AS` | Column aliasing | 1, 2 |
| `AND` / `OR` | Logical operators | 1, 2 |
| `IN` / `NOT IN` | Set membership | 1, 2 |
| `BETWEEN` | Range filter | 1 |
| `ORDER BY` | Sort results | 2 |
| `ASC` / `DESC` | Sort direction | 2 |
| `LIMIT` | Restrict row count | 2 |
| `OFFSET` | Skip rows | 2 |
| `GROUP BY` | Aggregate grouping | 2 |
| `COUNT(*)` | Row counting | 2 |
| `ROUND()` | Round numbers | 2 |
| `SQRT()` | Square root | 1, 2 |
| `ON CONFLICT` | Upsert handling | 1 |
| `RETURNING` | Return inserted data | 1 |

---

## 🚀 Getting Started

### Prerequisites
- PostgreSQL 12+ installed
- psql CLI or any PostgreSQL client (pgAdmin, DBeaver, etc.)

### Setup
1. Clone this repository:
   ```bash
   git clone https://github.com/DebjitDebjit2004/PostgreSQL---Commands---Revision.git
   cd PostgreSQL---Commands---Revision
   ```

2. Connect to PostgreSQL:
   ```bash
   psql -U your_username -d your_database
   ```

3. Run the SQL files in order:
   ```bash
   \i 'Day - 1'
   \i 'Day - 2'
   ```

---

## 📁 Repository Structure

```
PostgreSQL-Database/
├── Day - 1          # SQL fundamentals: CRUD, WHERE, operators
├── Day - 2          # Sorting, pagination, grouping
└── README.md        # This documentation
```

---

## 🤝 Contributing

Feel free to fork this repository and add your own SQL learning notes!

---

## 📝 License

This project is open source and available under the [MIT License](LICENSE).

---

## 👤 Author

**Debjit Das**

- GitHub: [@DebjitDebjit2004](https://github.com/DebjitDebjit2004)

---

<p align="center">
  <i>⭐ Star this repository if you find it helpful!</i>
</p>
