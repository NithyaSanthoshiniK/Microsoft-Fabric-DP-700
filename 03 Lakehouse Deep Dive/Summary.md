# Microsoft Fabric — Session 3: Lakehouse Deep Dive

Notes from the third session of the Azure Data Engineer / Microsoft Fabric course. This session goes deep into lakehouse internals — files vs. delta tables, the medallion architecture, what makes delta tables special (ACID, schema enforcement, time travel), and how to query a lakehouse via the SQL analytical endpoint using SSMS.

## Table of Contents
- [Recap of Session 2](#recap-of-session-2)
- [Lakehouse Architecture: Files vs Tables](#lakehouse-architecture-files-vs-tables)
- [Files vs Tables — Key Differences](#files-vs-tables--key-differences)
- [What Is a Delta Table?](#what-is-a-delta-table)
- [ACID Transactions Explained](#acid-transactions-explained)
- [Delta Table Benefits](#delta-table-benefits)
- [SQL Analytical Endpoint](#sql-analytical-endpoint)
- [Hands-On Walkthrough](#hands-on-walkthrough)
- [Lakehouse vs Warehouse (Preview)](#lakehouse-vs-warehouse-preview)
- [Assignment](#assignment)
- [Next Session](#next-session)

## Recap of Session 2
- Every Fabric artifact (lakehouse, pipeline, notebook, etc.) requires a **workspace** first — workspaces are typically created one per environment (dev/test/prod).
- A lakehouse can store both **unstructured data** (via the Files section) and **structured data** (via schemas/tables).
- Covered: creating a Fabric account, portal tour, workspace roles, creating a lakehouse, and building a first pipeline to copy files between folders.

## Lakehouse Deep dive
<img width="2752" height="1536" alt="Cloud_Lakehouse_Architecture_and_Power" src="https://github.com/user-attachments/assets/9c016365-7120-4ea7-9823-0f725b107194" />


## Lakehouse Architecture: Files vs Tables
Everything in a lakehouse — whether stored as a file or a table — physically lives in **OneLake**. A lakehouse is essentially a folder inside OneLake, and within it:
- **Files** subfolder → raw data in any format (CSV, JSON, Parquet, logs, images, audio, video)
- **Tables** subfolder → structured data stored in **Delta format**

<img width="627" height="580" alt="Screenshot 2026-08-25 at 3 40 19 PM" src="https://github.com/user-attachments/assets/ae15241c-3ee0-4d84-b1d1-6485b67ff41e" />

Tables are not created independently — they're built **from** files. Once created, a table's underlying data is automatically converted to Parquet + a Delta transaction log.

This data can be accessed by Spark notebooks, pipelines, Power BI, SQL analytics, and Synapse — all reading from the same underlying OneLake storage.


## Files vs Tables — Key Differences

| | Files | Tables |
|---|---|---|
| **Format** | Any raw format (CSV, JSON, Parquet, etc.) | Delta format only |
| **Schema enforcement** | None — structure can vary | Enforced — fixed column structure |
| **Queryable via SQL** | No — requires Spark for transformation | Yes — via SQL analytical endpoint |
| **Typical use** | Ingestion / raw landing zone | Business-ready data for reporting |
| **Consumed by** | Spark notebooks (PySpark) | Power BI, SQL/BI users |

## What Is a Delta Table?
Analogy used in the session: a normal Excel file vs. a **"smart" Excel file**.
- A normal file has no change tracking — accidental deletes can't be recovered, concurrent edits from two people can conflict silently, and performance degrades as the file grows.
- A **delta table** ("smart Excel file") tracks every change in a transaction log, supports only one writer at a time per operation, and can restore prior versions.

**Definition:** a delta table is an **open-source file format** (Parquet data + a Delta transaction log) that brings ACID transactions, CRUD operations (update/delete/merge), schema enforcement, schema evolution, and time travel to a data lake.

## ACID Transactions Explained
Illustrated with a bank-transfer example (₹1,000 → transferring ₹500 to a friend):

- **Atomicity** — the transfer either fully completes or fully rolls back; no partial states (money deducted but not credited).
- **Consistency** — data is never left in a half-written or invalid state.
- **Isolation** — concurrent operations (e.g., two people trying to use the same funds at once) are handled without conflicting; one is processed, the other is blocked/retried.
- **Durability** — once committed, data (or a payment) is safely persisted and won't be lost.

## Delta Table Benefits
- **Reliable transactions** — no more "stuck" states (compared to early payment-app failures where money left one account but never reached another).
- **Schema enforcement** — prevents bad/malformed data from being written; **schema evolution** allows intentional structure changes.
- **Time travel** — restore a table to a previous point in time after accidental deletes/updates.
- **Faster queries** — optimizations (partitioning, small-file handling) improve read performance even as data grows.
- Supports **update, delete, and merge** operations — not possible on plain files.

## SQL Analytical Endpoint
- Every lakehouse **automatically** gets a **SQL analytical endpoint** — no manual setup required.
- It's a **read-only** SQL interface over the delta tables (TSQL) — no data movement, duplication, or DML (INSERT/UPDATE/DELETE) is supported.
- Primary users: BI developers, SQL developers, business analysts — anyone who prefers SQL over Spark/PySpark.
- Data engineers, by contrast, typically use **Spark notebooks** for transformation work.
- Connect via **SSMS** (SQL Server Management Studio) using the endpoint URL as the server name, with Microsoft Entra MFA authentication.
- ⚠️ **Schema and object names are case-sensitive** in lakehouse/warehouse SQL (unlike traditional SQL Server).
- No direct ODBC/JDBC connection string is available for external tools — external access requires OneLake connector modules.

## Hands-On Walkthrough
1. Created a new workspace (`cloudpandit-dev-workspace-2`) attached to the Fabric trial.
2. Created a new lakehouse (`dev-lakehouse-0805`) — noted that the **SQL analytical endpoint is generated automatically** the moment the lakehouse is created.
3. Implemented a simple **medallion architecture** folder structure under Files:
   - `global/India/bronze/emp`, `global/India/bronze/department` (raw data)
   - `global/India/silver` (cleaned data)
   - `global/India/gold` (final, reporting-ready data)
4. Uploaded `employees.csv` and `departments.csv` into their respective bronze folders.
5. Created **delta tables** from the CSVs via **Load to Tables → New Table**:
   - `department` table in the default `dbo` schema
   - `employee` table in a new `salesLT` schema (demonstrating schema case-sensitivity — `salesLT` must be referenced in the exact case it was created)
6. Verified that table data is stored as **Parquet + Delta log** by switching from Table view to File view.
7. Connected to the lakehouse from **SSMS** via the SQL analytical endpoint (Entra MFA login).
8. Ran SQL queries against the tables:
   - `SELECT TOP 1000` on individual tables
   - `INNER JOIN` between `employee` and `department` on `department_id`
   - Aggregate query with `GROUP BY department_name` computing total, max, and mean salary per department
9. Attempted a Spark notebook read of the same data (attached lakehouse to a new notebook, used `spark.read.format("csv")`) — hit a temporary trial-capacity limitation, illustrating the Spark/notebook path data engineers use instead of the SQL endpoint.
10. Demonstrated that **DML statements (UPDATE/DELETE) are not supported** against lakehouse tables via the SQL endpoint — attempting an `UPDATE` or `DELETE` returns an error, since the endpoint is read-only. (Warehouse tables, covered next session, do support full read/write.)

## Lakehouse vs Warehouse (Preview)

<img width="1200" height="630" alt="Lakehouse" src="https://github.com/user-attachments/assets/0d53a089-e678-47c5-aa98-ed17b6f05a0c" />

Briefly flagged as a key difference to be covered in the next session: lakehouse tables are **read-only** via the SQL endpoint, while **warehouse tables support full CRUD** (read and write) operations.

## Assignment
- Create a new lakehouse named `retail_lakehouse`.
- Create `bronze`, `silver`, and `gold` folders.
- Upload `customer.csv`, `product.csv`, and `sales.csv`.
- Create delta tables from each: `customer`, `product`, `sales`.
- Connect to the lakehouse from SSMS and run basic SQL queries against the new tables.

## Next Session
**Microsoft Fabric Warehouse** — warehouse architecture, creating your first warehouse, warehouse components and SQL endpoint, full CRUD support on warehouse tables (vs. read-only lakehouse tables), installing and using **OneLake File Explorer**, and lakehouse-vs-warehouse best practices/use cases.
