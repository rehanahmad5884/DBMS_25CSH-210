# DBMS Lab Experiments — 25CSH-210

A collection of the 10 practical experiments for the **Database Management Systems (25CSH-210)** lab course. The work progresses from ER modelling and schema design, through SQL querying, views, triggers and normalization, to transactions and query optimization. All code is written for **MySQL 8.0+**.

> **Student:** Rehan Ahmad  |  **UID / Roll No.:** 25LBCS3257  |  **Section / Group:** C/B

---

## Table of Contents

- [Repository Structure](#repository-structure)
- [Experiment Summary](#experiment-summary)
- [Experiment Details](#experiment-details)
- [Prerequisites](#prerequisites)
- [How to Run](#how-to-run)
- [Execution Notes](#execution-notes)

---

## Repository Structure

```
DBMS_25CSH-210/
├── README.md
├── Experiment_1.pdf            # ER diagram – Indian E-Commerce platform (handwritten)
├── Experiment_2.pdf            # ER → Relational schema + MySQL implementation (handwritten)
├── Experiment_3/
│   ├── Experiment_3.code       # Schema + data + all queries
│   └── Experiment_3.Queries    # Queries only
├── Experiment_4/
│   ├── Experiment_4.Code
│   └── Experiment_4.Queries
├── Experiment_5/
│   ├── Experiment_5.code
│   └── Experiment_5.Queries
├── Experiment_6/
│   ├── Experiment_6.code
│   └── Experiment_6.Queries
├── Experiment_7/
│   ├── Experiment_7.code
│   └── Experiment_7.Queries.txt
├── Experiment_8/
│   ├── Experiment_8.code
│   └── Experiment_8.Queries.txt
├── Experiment_9/
│   ├── Experiment_9.code
│   └── Experiment_9.Queries.txt
└── Experiment_10/
    ├── Experiment_10.code
    └── Experiment_10.Queries.txt
```

**Naming convention:** in each folder, the `.code` file contains the *complete* script (database/table creation, sample data, and the queries), while the `.Queries` file contains *only* the queries, for quick reference. Experiments 1 and 2 are submitted as PDFs.

---

## Experiment Summary

| # | Topic | Database used | Key concepts |
|---|-------|---------------|--------------|
| 1 | ER diagram for an Indian E-Commerce platform | — | Entities, attributes, relationships, cardinality, keys |
| 2 | ER diagram → relational schema in MySQL | `ECommerceDB` | PK, FK, `NOT NULL`, `UNIQUE`, `ON DELETE CASCADE / SET NULL` |
| 3 | Basic SQL: filtering, aggregation, joins | `DBMS_Experiment3` | `WHERE`, aggregates, `GROUP BY`, `HAVING`, `CASE`, `ORDER BY`, `JOIN` |
| 4 | Selection, projection, grouping and joins (extended) | `DBMS_Experiment3` | Aggregation with `HAVING` + `ORDER BY`, `JOIN`, `LEFT JOIN` |
| 5 | Views and recursive CTE | `DBMS_Experiment3` | `CREATE VIEW`, updatable views, self-join, `WITH RECURSIVE` |
| 6 | Stored procedure, triggers, audit logging | `DBMS_Experiment3` | `CREATE PROCEDURE`, `BEFORE`/`AFTER` triggers, `SIGNAL` |
| 7 | Normalization (UNF → 1NF → 2NF → 3NF → BCNF) | `DBMS_Experiments` | Functional dependencies, lossless join, dependency preservation |
| 8 | Transactions and isolation levels | `BankingDB` | `COMMIT`, `ROLLBACK`, `SAVEPOINT`, isolation levels |
| 9 | Indexing on 5,000 records | `RailwayDB` | Single/composite indexes, `EXPLAIN`, `EXPLAIN ANALYZE` |
| 10 | Query optimization | `QueryOptimizationDB` | Index design, sargable predicates, before/after `EXPLAIN` |

---

## Experiment Details

### Experiment 1 — ER Diagram (E-Commerce Platform)
**Aim:** Design an ER diagram for an Indian e-commerce platform.
**Entities:** Customer, Seller, Product, Category, Order, Order_Item, Payment, Delivery, Address.
**Covered:** relationship cardinalities (e.g. Customer–Order 1:N, Order–Order_Item 1:N, Order–Payment 1:1, Order–Delivery 1:1, Product–Category N:1), primary/foreign keys, multi-valued attributes, and specialization of Product (Electronics, Clothing, Books).

### Experiment 2 — ER to Relational Schema
**Aim:** Convert the Experiment 1 ER diagram into a relational schema and implement it in MySQL.
**Covered:** `CREATE TABLE` statements for all nine entities, sample `INSERT` data, and constraint testing — invalid foreign key insertion is rejected, `ON DELETE CASCADE` removes dependent rows when a customer is deleted, and `ON DELETE SET NULL` nulls the seller reference on a product when a seller is deleted.

### Experiment 3 — Basic SQL Queries
**Schema:** `Department` (5 rows), `Project` (8 rows), `Employee` (30 rows) with foreign keys to both.
**16 queries** covering: salary filters, `COUNT / AVG / MAX / MIN / SUM`, `GROUP BY` and `HAVING`, `CASE` salary categories, `ORDER BY`, department-wise `JOIN`, and project-wise employee count with `LEFT JOIN`.

### Experiment 4 — Advanced Selection, Aggregation and Joins
Uses the same Employee/Department/Project schema. **15 queries** covering selection, projection, combined aggregates, `GROUP BY ... HAVING ... ORDER BY`, and department-/project-wise summaries using `JOIN` and `LEFT JOIN`.

### Experiment 5 — Views and Recursive CTE
- `Department_Salary_Summary` — per-department count, average, total, highest and lowest salary.
- `Employee_Hierarchy` — employees with their manager's name (self-join on `Manager_ID`).
- `Employee_Basic_View` — simple view used to test **view updatability** (`UPDATE` through the view).
- **Recursive CTE** `Reporting_Chain` — walks the management hierarchy and reports each employee's level.

### Experiment 6 — Stored Procedures and Triggers
- **Procedure** `transfer_employee(emp_id, new_dept_id)` — validates the employee and department exist (raises errors via `SIGNAL`), then updates the department.
- **Triggers:** `validate_salary_before_insert` and `validate_salary_before_update` reject salary ≤ 0; `salary_audit_after_update` logs old/new salary into `Employee_Audit`.
- **Edge cases tested:** invalid employee ID, invalid department ID, negative salary.

### Experiment 7 — Normalization
Starts with a denormalized `Sales_UNF` table and the functional dependencies:

```
Order_ID               → Order_Date, Customer_ID, Salesperson_ID
Customer_ID            → Customer_Name, Customer_City
Product_ID             → Product_Name, Unit_Price
Salesperson_ID         → Salesperson_Name
(Order_ID, Product_ID) → Quantity
```

Decomposed step by step into `Sales_Customer`, `Sales_Salesperson`, `Sales_Product`, `Sales_Order` and `Sales_OrderLine` (3NF/BCNF). Includes queries to verify **lossless join** (reconstruct the original data) and **dependency preservation** (keys remain unique).

### Experiment 8 — Transactions and Isolation Levels
Banking schema (`Bank_Account`, `Bank_Transactions`, InnoDB). Demonstrates a committed transfer, a rolled-back transfer, `SAVEPOINT` with partial rollback, switching isolation levels (`READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE`), and **two-session tests** showing non-repeatable reads under `READ COMMITTED` versus consistent reads under `REPEATABLE READ`.

### Experiment 9 — Indexing
Generates **5,000 `Railway_Booking` rows** using a recursive CTE, then compares query plans using `EXPLAIN` / `EXPLAIN ANALYZE` before indexing and after creating a single-column index (`Train_No`), a composite index (`Train_No, Journey_Date`) and a status index (`Booking_Status`).

### Experiment 10 — Query Optimization
Analyses five slow queries on a `Sales_Analytics` table (single filter, multiple filters, function on a filtered column, aggregation, filter + sort). Optimizations include composite indexes, rewriting `YEAR(Order_Date) = 2025` as a date range so the index can be used, and comparing before/after plans with `EXPLAIN` and `EXPLAIN ANALYZE`.

---

## Prerequisites

- **MySQL 8.0.18+** (needed for `WITH RECURSIVE`, `EXPLAIN ANALYZE`, `@@transaction_isolation`)
- A client such as MySQL Workbench, DBeaver, or the `mysql` command line
- Two simultaneous connections for the isolation-level tests in Experiment 8

---

## How to Run

Run each experiment's `.code` file from its folder:

```bash
# Command line
mysql -u root -p < Experiment_3/Experiment_3.code

# Or, inside the MySQL shell
SOURCE Experiment_3/Experiment_3.code;
```

In MySQL Workbench, open the file and click **Execute**. Run `Experiment_N.Queries` afterwards if you only want to re-run the queries.

**Recommended order:** 3 → 4 → 5 → 6 (they share `DBMS_Experiment3`), then 7, 8, 9 and 10 independently.

---

## Execution Notes

Things to be aware of when running the scripts:

1. **Experiment 4** re-creates `Department`, `Project` and `Employee` inside `dbms_experiment3`, so it errors if Experiment 3 was already run. Drop the tables first, or use a fresh database. The header mentions subqueries, `EXISTS`, `INTERSECT`/`EXCEPT` and `EXPLAIN`, but the script itself contains aggregation and join queries only.
2. **Experiment 5** uses an `Employee.Manager_ID` column that is **not** part of the Experiment 3 schema. Add it first, for example:
   ```sql
   ALTER TABLE Employee ADD COLUMN Manager_ID INT NULL;
   UPDATE Employee SET Manager_ID = NULL WHERE Emp_ID = 1;  -- top-level manager
   -- assign Manager_ID values for the other employees as required
   ```
   The `.code` file also contains stray Markdown code fences (```` ``` ````) and a trailing `don` that must be removed before execution. The `UPDATE` on `Employee_Basic_View` should be executed only once.
3. **Experiment 6** contains the same stray ```` ``` ```` fences inside the procedure body; remove them before running. The test `UPDATE`/`CALL` statements change Employee 1's department and salary.
4. **Experiment 7** uses its own database `DBMS_Experiments`; run `Experiment_7.code` first to create `Sales_UNF`.
5. **Experiment 8** two-session tests (Queries 5 and 6) require two separate connections.
6. **Experiment 10** — `Experiment_10.code` is currently a copy of the Experiment 9 setup script. The queries expect a `QueryOptimizationDB` database with a `Sales_Analytics` table (columns: `Record_ID`, `Customer_ID`, `Order_Date`, `Order_Status`, `Region`, `Product_Category`, `Quantity`, `Unit_Price`), so a setup script that creates and populates it must be run first.

---

## Technologies

![MySQL](https://img.shields.io/badge/MySQL-8.0+-4479A1?logo=mysql&logoColor=white)
![SQL](https://img.shields.io/badge/Language-SQL-orange)
