# Microsoft Fabric Master Program — Session 1
## Introduction to Data Engineering & Microsoft Fabric

---

## 1. What is Microsoft Fabric?

**Definition:** Microsoft Fabric is a **unified analytics platform** — it brings multiple analytical tools into **one single environment**.

**Before Fabric**, organizations used separate tools for each stage of a data project:

```
 Data Integration   →   Storage        →   Processing    →   Analytics       →   Reporting
 Azure Data Factory     Azure Data Lake     Databricks        Synapse          Power BI
```

**Problem with this setup:**
- Complex architecture (many tools to connect together)
- Higher cost (paying for/managing multiple services)
- Harder security (each service needs its own security setup)

**With Fabric**, all of this lives under a single umbrella:

```
                 ┌─────────────────────────────────────────┐
                 │            MICROSOFT FABRIC              │
                 │                                           │
   Sources  ───▶ │  Pipelines → One Lake → Notebooks →      │ ───▶  Power BI
                 │              (Storage)   Warehouse         │       Reports
                 └─────────────────────────────────────────┘
```

**Key benefit:** Organizations can **collect → store → process → analyze → visualize** data using one integrated service — easier development, faster integration, simpler analytics, better-managed cost and security.

---

## 2. What is Data Engineering?

**Definition:** Data engineering is the process of **collecting, processing, and preparing data** so it can be used for analysis and reporting.

### Why it's needed
Raw data coming straight from source systems (apps, transactions, sensors) is usually **not fit for reporting**. Common problems:

| Problem | Example |
|---|---|
| Missing values | Key fields left blank during a transaction |
| Duplicate records | User double-clicks "submit" → two orders created |
| Inconsistent formats | One system stores dates as `YYYY-MM-DD`, another as `MM-DD-YYYY` |

A data engineer's job is to catch and fix these issues so business teams can trust the data.

### Example: e-commerce order flow
```
Order System  ─┐
Payment System ─┼──▶  Data Lake (raw) ──▶ Clean & Transform ──▶ Warehouse ──▶ Power BI
Shipping System─┘                                                              Reports
```

### Role of a Data Engineer
1. **Collect** — gather data from multiple source systems (orders, payments, shipping, etc.)
2. **Clean** — remove duplicates, handle missing values, fix inconsistent formats
3. **Transform** — join/restructure data (e.g., join `Employee` table with `Department` table on `Department ID`)
4. **Load** — push the final, clean data into a data warehouse
5. **Enable reporting** — Power BI (or direct SQL queries) consumes this data for business decisions

---

## 3. ETL vs ELT

Two data integration approaches:

### ETL (Extract → Transform → Load)
```
Source Systems ──▶ Extract ──▶ Transform ──▶ Load ──▶ Data Warehouse ──▶ Power BI
                              (SSIS, DataStage,
                               Informatica — on-prem tools)
```
- Transformation happens **before** loading
- Data lands directly in the warehouse (structured only)
- Traditional / on-premise approach

### ELT (Extract → Load → Transform)
```
Source Systems ──▶ Extract ──▶ Load ──▶ Data Lake ──▶ Transform ──▶ Data Lake/Warehouse ──▶ Power BI
                                        (raw data)    (Databricks, Synapse,
                                                        Fabric, Snowflake)
```
- Data is loaded **as-is** (raw, any format) into a data lake first
- Transformation happens **after** loading, using compute engines like Spark
- Modern cloud/big-data approach — **this is what Fabric uses**

| | ETL | ELT |
|---|---|---|
| Order | Transform before load | Load before transform |
| Storage | Data Warehouse | Data Lake |
| Tools | SSIS, DataStage, Informatica (on-prem) | Databricks, Synapse, Fabric, Snowflake (cloud) |
| Best for | Traditional BI | Big data / cloud analytics |

**Why ELT for Fabric:** raw data can be huge and come in many formats — a data lake stores it cheaply and flexibly, then Fabric's compute tools (Spark notebooks) transform it afterward.

---

## 4. Batch vs Streaming Data Processing

### Batch Processing
- Data is processed at **scheduled intervals** (every 30 min / hourly / daily / weekly / monthly)
- **Examples:** credit card bills, electricity bills, monthly salaries, EMI payments, sales reports

```
Data arrives → wait for schedule → process in batch (e.g., once a day)
```

### Streaming Processing
- Data is processed **in real time**, as soon as it arrives — no waiting
- **Examples:** stock market data, IoT sensor data, flight tracking, fraud detection

```
Data arrives → processed immediately, continuously
```

**Real-world example — fraud detection:** if a bank account makes a transaction above a set threshold (e.g., ₹50,000+), the system flags it instantly and may trigger a verification call — this only works with streaming processing.

| | Batch | Streaming |
|---|---|---|
| Timing | Scheduled intervals | Real-time, continuous |
| Examples | Bills, salaries, EMIs, sales reports | Stock prices, IoT sensors, fraud detection, flight tracking |

---

## 5. Microsoft Fabric Architecture
<p align="center">
  <img src="https://learn.microsoft.com/en-us/fabric/fundamentals/media/microsoft-fabric-overview/onelake-architecture.png" alt="OneLake Architecture" width="600"/>
</p>

**One Lake** is the foundation — a **single centralized storage layer** for the entire organization (essentially the same concept as Azure Data Lake Storage, renamed/unified under Fabric).

```
                              ┌────────────────────────────┐
                              │          ONE LAKE            │
                              │  (single centralized storage)│
                              │                              │
                              │  ┌──────────┐  ┌──────────┐  │
                              │  │Lakehouse │  │Warehouse │  │
                              │  │ (files + │  │ (tables  │  │
                              │  │  tables) │  │  only)   │  │
                              │  └──────────┘  └──────────┘  │
                              │  ┌──────────┐  ┌──────────┐  │
                              │  │   KQL    │  │ Datasets │  │
                              │  │ Database │  │(for Power│  │
                              │  │(real-time│  │   BI)    │  │
                              │  └──────────┘  └──────────┘  │
                              └────────────────────────────┘
```

> All of these (Lakehouse, Warehouse, KQL Database, Datasets) are essentially **folders/components within One Lake** — not separate storage systems.

### End-to-end component flow

```
Source Systems
     │
     ▼
Pipelines  ─────────────▶  data collection / integration (like Data Factory)
     │
     ▼
One Lake  ──────────────▶  centralized raw storage
     │
     ▼
Notebooks  ─────────────▶  Spark-based transformation (clean, dedupe, standardize)
  (Python, PySpark, SQL,
   Scala supported)
     │
     ▼
Warehouse  ─────────────▶  structured, query-ready data (via T-SQL)
     │
     ▼
Power BI  ──────────────▶  dashboards & reports for business decisions
```

Additional branches off the cleaned data:
- **Machine Learning / Data Science teams** → predictive analysis (e.g., sales forecasting)
- **Streaming data** → real-time processing (e.g., live fraud detection)
- Both can also feed into **Power BI** for reporting

### Component summary

| Component | Purpose |
|---|---|
| **One Lake** | Centralized storage layer for the whole organization (single logical data lake) |
| **Lakehouse** | Stores structured, semi-structured, and unstructured data (files + tables) |
| **Warehouse** | Stores structured data only (tables); SQL-based analytics |
| **Pipelines** | Data integration & orchestration — collect data, schedule jobs, automate workflows |
| **Notebooks** | Spark-based data transformation (Python, PySpark, SQL, Scala) |
| **Power BI** | Data visualization & reporting — dashboards, business insights |
| **Shortcuts** | Link to data elsewhere in One Lake without copying/duplicating it |

**Why One Lake instead of separate lakes per department (finance, orders, shipping)?**
Because departments often need each other's data (e.g., shipping needs payment confirmation before shipping an order). Separate lakes led to duplicated data. With One Lake, you can still organize data into folders (e.g., a "payment lakehouse," a "shipping lakehouse") but use **shortcuts** to reference data instead of copying it.

---

## 6. Quick Recap — Key Definitions

- **Microsoft Fabric:** Unified analytics platform combining data integration, storage, processing, and reporting tools into one environment.
- **Data Engineering:** Collecting, processing, and preparing data for analytics/reporting.
- **Data Engineer's role:** Collect → Clean → Transform → Load → Enable reporting.
- **ETL:** Transform *before* loading (traditional, warehouse-based).
- **ELT:** Transform *after* loading (modern, data lake-based) — used in Fabric.
- **Batch processing:** Scheduled intervals (bills, salaries).
- **Streaming processing:** Real-time, continuous (stock data, fraud detection, IoT).
- **One Lake:** Fabric's single centralized storage layer.

---

## 7. Assignment (for next session)

1. Be able to define **Microsoft Fabric**.
2. Explain the difference between **ETL vs ELT**.
3. Explain the difference between **batch vs streaming** processing.
4. **Draw the Fabric architecture** from memory (One Lake → Lakehouse/Warehouse/KQL DB/Datasets → Pipelines → Notebooks → Power BI).

---

## 8. What's Next (Session 2 Preview)

- Creating a Fabric account (free trial) and exploring the Fabric portal
- Creating a workspace and understanding workspace roles
- Creating your first **Lakehouse** and uploading a CSV file
- Exploring **One Lake**
- Building your first **pipeline** to move data between two lakehouses

---

## 9. Session Quiz Answers (Self-Check)

| # | Question | Answer |
|---|---|---|
| 1 | What is Microsoft Fabric? | Unified analytics platform |
| 2 | What does Fabric do with data tools? | Combines multiple data tools into a single platform |
| 3 | Primary goal of data engineering | Collect, process, and prepare data for analytics |
| 4 | NOT a common problem in raw data | "Perfectly validated data" (the real problems are missing values, duplicates, inconsistent formats) |
| 5 | In ETL, when does transformation happen? | Before loading the data |
| 6 | Approach used in big data/cloud analytics | ELT |
| 7 | Best method for real-time fraud detection | Streaming data processing |
| 8 | Fabric's central storage layer | One Lake |
| 9 | Component for workflow automation & data integration | Pipelines (Data Factory) |
| 10 | Component for dashboards & reports | Power BI |

*(Score 8/10 or higher = solid understanding of this session; below that, review the basics before moving to hands-on practice.)*
