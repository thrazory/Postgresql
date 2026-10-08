# postgresql-studies

Personal study notes and SQL scripts for learning PostgreSQL from scratch.  
Structured by topic — from basic DDL to advanced query techniques.

---

## Topics Covered

- [01 · CRUD & DDL](#01--crud--ddl)
- [02 · Constraints](#02--constraints)
- [03 · DML — INSERT, UPDATE, DELETE](#03--dml--insert-update-delete)
- [04 · SELECT & Filtering](#04--select--filtering)
- [05 · JOINs](#05--joins)
- [06 · Aggregations & GROUP BY](#06--aggregations--group-by)
- [07 · Subqueries & CTEs](#07--subqueries--ctes)
- [08 · Window Functions](#08--window-functions)
- [09 · Indexes & Performance](#09--indexes--performance)
- [10 · Views & Materialized Views](#10--views--materialized-views)
- [11 · Stored Procedures & Functions](#11--stored-procedures--functions)
- [12 · Transactions & Locks](#12--transactions--locks)
- [13 · Data Types](#13--data-types)
- [14 · Schema Design & Normalization](#14--schema-design--normalization)

---

## Structure

```
postgresql-studies/
├── 01_crud_ddl/
│   └── crud_ddl.sql
├── 02_constraints/
│   └── constraints.sql
├── 03_dml/
│   └── dml.sql
├── 04_select_filtering/
│   └── select_filtering.sql
├── 05_joins/
│   └── joins.sql
├── 06_aggregations/
│   └── aggregations.sql
├── 07_subqueries_ctes/
│   └── subqueries_ctes.sql
├── 08_window_functions/
│   └── window_functions.sql
├── 09_indexes_performance/
│   └── indexes.sql
├── 10_views/
│   └── views.sql
├── 11_procedures_functions/
│   └── procedures_functions.sql
├── 12_transactions/
│   └── transactions.sql
├── 13_data_types/
│   └── data_types.sql
└── 14_schema_design/
    └── schema_design.sql
```

## 01 · CRUD & DDL

Data Definition Language — creating, altering, and dropping database objects.

```sql
CREATE DATABASE my_database;
DROP DATABASE my_database;

CREATE TABLE table_name (column1 type1, column2 type2);
ALTER TABLE table_name ADD COLUMN new_column type;
DROP TABLE table_name;
TRUNCATE TABLE table_name;
```

📁 [`01_crud_ddl/`](./01_crud_ddl/)

---

## 02 · Constraints

Rules that enforce data integrity at the schema level.

| Constraint      | Description                                        |
|-----------------|----------------------------------------------------|
| `NOT NULL`      | Column cannot store null values                    |
| `PRIMARY KEY`   | Unique, non-null identifier for each row           |
| `FOREIGN KEY`   | Enforces referential integrity between tables      |
| `UNIQUE`        | All values in the column must be distinct          |
| `CHECK`         | Validates a condition before inserting/updating    |
| `DEFAULT`       | Assigns a default value when none is provided      |

```sql
CREATE TABLE customers (
    id         SERIAL PRIMARY KEY,
    name       VARCHAR(100) NOT NULL,
    email      VARCHAR(100) UNIQUE NOT NULL,
    age        INT CHECK (age >= 0),
    created_at DATE DEFAULT CURRENT_DATE
);
```

📁 [`02_constraints/`](./02_constraints/)

---

## 03 · DML — INSERT, UPDATE, DELETE

Data Manipulation Language — adding, modifying, and removing records.

```sql
-- INSERT
INSERT INTO table_name (col1, col2) VALUES (val1, val2);

-- UPDATE
UPDATE table_name SET col1 = val1 WHERE condition;

-- DELETE
DELETE FROM table_name WHERE condition;
```

📁 [`03_dml/`](./03_dml/)

---

## 04 · SELECT & Filtering

Querying and filtering data.

```sql
SELECT col1, col2 FROM table_name;
SELECT DISTINCT col1 FROM table_name;

-- Filtering
WHERE col1 = 'value'
WHERE col1 BETWEEN 10 AND 50
WHERE col1 IN ('a', 'b', 'c')
WHERE col1 LIKE '%pattern%'
WHERE col1 IS NULL

-- Sorting & Limiting
ORDER BY col1 ASC / DESC
LIMIT 10 OFFSET 20
```

📁 [`04_select_filtering/`](./04_select_filtering/)

---

## 05 · JOINs

Combining data from multiple tables.

| Type           | Returns                                              |
|----------------|------------------------------------------------------|
| `INNER JOIN`   | Rows with matching values in both tables             |
| `LEFT JOIN`    | All rows from left + matched rows from right         |
| `RIGHT JOIN`   | All rows from right + matched rows from left         |
| `FULL JOIN`    | All rows from both tables                            |
| `CROSS JOIN`   | Cartesian product of both tables                     |
| `SELF JOIN`    | Table joined with itself                             |

```sql
SELECT a.name, b.course
FROM students a
INNER JOIN enrollments b ON a.id = b.student_id;
```

📁 [`05_joins/`](./05_joins/)

---

## 06 · Aggregations & GROUP BY

Summarizing data with aggregate functions.

```sql
SELECT department, COUNT(*), AVG(salary), SUM(salary), MAX(salary), MIN(salary)
FROM employees
GROUP BY department
HAVING AVG(salary) > 5000
ORDER BY AVG(salary) DESC;
```

📁 [`06_aggregations/`](./06_aggregations/)

---

## 07 · Subqueries & CTEs

Nested queries and Common Table Expressions.

```sql
-- Subquery
SELECT name FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

-- CTE
WITH high_earners AS (
    SELECT * FROM employees WHERE salary > 8000
)
SELECT department, COUNT(*) FROM high_earners GROUP BY department;
```

📁 [`07_subqueries_ctes/`](./07_subqueries_ctes/)

---

## 08 · Window Functions

Calculations across a set of rows related to the current row.

```sql
SELECT
    name,
    department,
    salary,
    RANK()        OVER (PARTITION BY department ORDER BY salary DESC),
    ROW_NUMBER()  OVER (PARTITION BY department ORDER BY salary DESC),
    LAG(salary)   OVER (PARTITION BY department ORDER BY salary),
    LEAD(salary)  OVER (PARTITION BY department ORDER BY salary),
    SUM(salary)   OVER (PARTITION BY department)
FROM employees;
```

📁 [`08_window_functions/`](./08_window_functions/)

---

## 09 · Indexes & Performance

Speeding up queries with indexes.

```sql
-- Create index
CREATE INDEX idx_name ON table_name (column);
CREATE UNIQUE INDEX idx_email ON customers (email);

-- Drop index
DROP INDEX idx_name;

-- Analyze query plan
EXPLAIN ANALYZE SELECT * FROM customers WHERE email = 'test@email.com';
```

📁 [`09_indexes_performance/`](./09_indexes_performance/)

---

## 10 · Views & Materialized Views

Saving query logic as reusable objects.

```sql
-- View (always up to date)
CREATE VIEW active_customers AS
SELECT * FROM customers WHERE active = TRUE;

-- Materialized View (stores result, must refresh)
CREATE MATERIALIZED VIEW sales_summary AS
SELECT product_id, SUM(amount) FROM sales GROUP BY product_id;

REFRESH MATERIALIZED VIEW sales_summary;
```

📁 [`10_views/`](./10_views/)

---

## 11 · Stored Procedures & Functions

Reusable logic stored inside the database.

```sql
-- Function
CREATE OR REPLACE FUNCTION get_total_sales(p_product_id INT)
RETURNS NUMERIC AS $$
BEGIN
    RETURN (SELECT SUM(amount) FROM sales WHERE product_id = p_product_id);
END;
$$ LANGUAGE plpgsql;

-- Procedure
CREATE OR REPLACE PROCEDURE update_price(p_id INT, p_price NUMERIC)
LANGUAGE plpgsql AS $$
BEGIN
    UPDATE products SET price = p_price WHERE id = p_id;
END;
$$;
```

📁 [`11_procedures_functions/`](./11_procedures_functions/)

---

## 12 · Transactions & Locks

Ensuring data consistency with atomic operations.

```sql
BEGIN;
    UPDATE accounts SET balance = balance - 500 WHERE id = 1;
    UPDATE accounts SET balance = balance + 500 WHERE id = 2;
COMMIT;

-- Rollback on error
BEGIN;
    DELETE FROM orders WHERE id = 99;
ROLLBACK;

-- Savepoint
SAVEPOINT my_savepoint;
ROLLBACK TO my_savepoint;
```

📁 [`12_transactions/`](./12_transactions/)

---

## 13 · Data Types

Common PostgreSQL data types.

| Category  | Types                                              |
|-----------|----------------------------------------------------|
| Numeric   | `INT`, `BIGINT`, `NUMERIC`, `FLOAT`, `SERIAL`      |
| Text      | `VARCHAR(n)`, `CHAR(n)`, `TEXT`                    |
| Date/Time | `DATE`, `TIME`, `TIMESTAMP`, `INTERVAL`            |
| Boolean   | `BOOLEAN`                                          |
| JSON      | `JSON`, `JSONB`                                    |
| Other     | `UUID`, `ARRAY`, `ENUM`                            |

📁 [`13_data_types/`](./13_data_types/)

---

## 14 · Schema Design & Normalization

Best practices for structuring relational databases.

- **1NF** — Atomic values, no repeating groups  
- **2NF** — No partial dependencies on composite keys  
- **3NF** — No transitive dependencies  
- **Star Schema** — Fact table + dimension tables (used in data warehouses)  
- **Snowflake Schema** — Normalized dimension tables  

📁 [`14_schema_design/`](./14_schema_design/)

---

## Stack

- **Database:** PostgreSQL 16  
- **Client:** pgAdmin / DBeaver / psql  

---

## Author

**Thiago Vieira** · [LinkedIn](https://www.linkedin.com/in/thiagotsmv) · [GitHub](https://github.com/thrazory)
