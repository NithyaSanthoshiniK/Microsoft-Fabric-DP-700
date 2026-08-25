# Microsoft Fabric — Session 2: Getting Started with Fabric

Notes from the second session of the Azure Data Engineer / Microsoft Fabric course. Session 1 covered Fabric fundamentals, ETL vs ELT, and batch vs streaming; this session moves into hands-on setup — creating a Fabric account, exploring the portal, and building the first pipeline.

## Table of Contents
- [Recap of Session 1](#recap-of-session-1)
- [Creating a Fabric Account](#creating-a-fabric-account)
- [Fabric Portal Tour](#fabric-portal-tour)
- [Workspaces](#workspaces)
- [Workspace Roles](#workspace-roles)
- [Lakehouse](#lakehouse)
- [Data Pipelines](#data-pipelines)
- [Hands-On Walkthrough](#hands-on-walkthrough)
- [Assignment](#assignment)
- [Next Session](#next-session)

## Recap of Session 1
- What is Fabric and why it exists — a **unified analytics platform** that combines previously separate tools (Data Factory, Data Lake, Databricks, Power BI) into one environment ("all-in-one book").
- Role of a data engineer: collect, clean, transform, and prepare data for reporting.
- Why data engineers are needed: source data is often incomplete, duplicated, inconsistently formatted, or spread across multiple systems.
- **ETL vs ELT**: ETL transforms before loading; ELT (the common pattern in cloud/Fabric projects) loads first, then transforms.
- **Batch vs streaming data**: batch = periodic/historical (bills, statements); streaming/real-time = continuous (ATM transactions, stock market data, IoT, flight tracking).
- Fabric architecture components: OneLake, Lakehouse, Warehouse, KQL database, Spark/SQL/KQL transformations, pipelines, notebooks, Power BI reporting.

## Creating a Fabric Account
Two ways to get access:
1. **Azure subscription route** — requires a Microsoft account plus an Azure free-trial subscription (credit/debit card required).
2. **Organization account route** — log in directly with a work email (e.g. `name@company.com`).

Either path unlocks a **60-day free trial** of Fabric capabilities.

Portal URL: **`app.fabric.microsoft.com`**

## Fabric Portal Tour
Key sections in the portal:

| Section | Purpose |
|---|---|
| **Home** | Central dashboard — recent items and quick-create suggestions |
| **Workspaces** | Project-based containers for Fabric items |
| **Copilot** | AI assistant for generating SQL, pipelines, and analysis in natural language (not available on free trial) |
| **OneLake catalog** | Centralized catalog to browse/discover data across OneLake |
| **Monitor** | Dashboard for tracking pipeline, notebook, and job status/history |
| **My Workspace** | Personal default workspace for building/testing before sharing |
| **Settings/Admin** | Connections, admin controls, downloadable tools (Power BI Desktop, OneLake Explorer, data gateway) |
| **Focus mode** | Hides side panels to reduce distraction |

## Workspaces
A workspace is a **project-based container** for all Fabric objects (lakehouses, warehouses, pipelines, notebooks, semantic models, reports). Without a workspace, no other Fabric item can be created.

**Naming convention example:**
```
<client>-<project>-dev-workspace
<client>-<project>-test-workspace
<client>-<project>-prod-workspace
```

When creating a workspace, it must be attached to the **Fabric trial capacity** (not Power BI Pro) to unlock the 60-day free features.

## Workspace Roles

| Role | Permissions |
|---|---|
| **Admin** | Full control, including user management — reserved for architects/leads |
| **Member** | Full create/edit/share access to items and data |
| **Contributor** | Create/edit/share items, but limited data-sharing permissions |
| **Viewer** | Read-only — typically assigned to support/monitoring staff |

## Lakehouse
A **lakehouse** combines the flexibility of a data lake with the performance of a data warehouse:
- Stores structured, semi-structured, and unstructured data (CSV, JSON, Parquet, images, logs, Delta tables).
- Built on top of **OneLake**, which provides unified, centralized, ACID-compliant storage.
- Supports Spark, SQL, and notebook-based transformations.
- Organizes structured data via **schemas → tables**, and unstructured/file data via a **Files** section with folders.

**ACID recap:**
- **Atomicity** — failed writes roll back completely
- **Consistency** — no partially written records
- **Isolation** — concurrent writes don't conflict
- **Durability** — committed data isn't lost

## Data Pipelines
A pipeline is a **no-code, drag-and-drop visual workflow** used to automate, orchestrate, and schedule data movement and processing.

**Capabilities:**
- Copy data from sources (on-prem SQL Server, REST APIs, Azure SQL, Snowflake, etc.) into a lakehouse/warehouse.
- Orchestrate multi-step sequences (e.g., ingest → transform via notebook → refresh Power BI report).
- Schedule recurring runs.
- Call notebooks, stored procedures, or dataflows for transformations.

**Core components:**
- **Activities** — Copy, Notebook, Stored Procedure, Dataflow, etc.
- **Connections** — links to source/destination systems
- **Triggers** — scheduling
- **Parameters** — make pipelines dynamic/reusable across many tables
- **Monitor tab** — tracks execution status, duration, and errors

## Hands-On Walkthrough
Steps performed live in the session:
1. Logged into `app.fabric.microsoft.com`.
2. Created a new workspace (e.g. `cloudpandit-education-dev-workspace`) attached to the Fabric trial.
3. Created a new **Lakehouse** (schemas enabled by default; `dbo` schema auto-created).
4. Created an **`input`** folder under Files and uploaded sample CSV files.
5. Created an **`output`** folder under Files.
6. Built a new **pipeline** (`pipeline-for-lakehouse-data-ingestion-daily`) using a **Copy Data** activity:
   - **Source**: Lakehouse → `input` folder → wildcard file selection (`*`)
   - **Destination**: Lakehouse → `output` folder
7. Saved, validated, and ran the pipeline — copy activity succeeded, files appeared in `output`.

## Assignment
- Log into the shared Fabric trial account.
- Create a new workspace.
- Create a lakehouse.
- Create a custom input/output folder pair and upload a CSV file.
- Build a first pipeline with a Copy Data activity moving files from the input to the output folder.
- Run the pipeline and confirm the copy succeeds.

## Next Session
**Lakehouse Deep Dive** — lakehouse architecture, files vs. tables, creating Delta tables, querying via SQL, connecting to the lakehouse's SQL analytics endpoint from SSMS, and folder/data organization best practices.

---
