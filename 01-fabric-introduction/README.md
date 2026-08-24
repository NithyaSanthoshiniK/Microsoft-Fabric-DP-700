# Microsoft Fabric – Introduction to Data Engineering

## 📚 Source

**Course:** Microsoft Fabric Full Course for Beginners  
**Topic:** Introduction to Data Engineering | ETL vs ELT

🎥 [YouTube Video](https://youtu.be/Ggs4iN6zdEs)

---

## 🎯 Learning Objectives

By completing this lesson, I learned:

- What Microsoft Fabric is
- What Data Engineering means
- Why organizations use data pipelines
- ETL vs ELT
- The role of OneLake
- The concept of a Lakehouse
- How data moves through a modern data platform
- How Microsoft Fabric integrates data engineering and analytics

---

## 🏗️ Microsoft Fabric

Microsoft Fabric is an end-to-end analytics platform that brings together:

- Data Engineering
- Data Factory
- Data Science
- Data Warehouse
- Real-Time Intelligence
- Power BI
- Databases

These workloads operate over a shared data foundation called **OneLake**.

---

## 🔄 Modern Data Engineering Flow

Data Sources
     ↓
Data Ingestion
     ↓
OneLake
     ↓
Data Transformation
     ↓
Lakehouse / Warehouse
     ↓
Analytics
     ↓
Power BI / Applications

## 🔁 ETL vs ELT
ETL

ETL = Extract → Transform → Load

Source
  ↓
Extract
  ↓
Transform
  ↓
Load
  ↓
Destination

Transformation happens before the data is loaded into the destination.

ELT

ELT = Extract → Load → Transform

Source
  ↓
Extract
  ↓
Load
  ↓
Data Lake / Lakehouse
  ↓
Transform
  ↓
Analytics

Raw data is loaded first and transformed later using the processing capabilities of the target platform.

## ⚖️ ETL vs ELT
Feature	ETL	ELT
Transformation	Before loading	After loading
Raw data retained	Usually not	Usually yes
Scalability	Traditional	Cloud-native
Best suited for	Controlled pipelines	Large-scale cloud data
Processing	ETL engine	Target platform
Typical use	Data warehouses	Data lakes / lakehouses

Microsoft Fabric Data Factory supports both ETL and ELT approaches.

## 🏞️ OneLake

OneLake is the unified data lake foundation of Microsoft Fabric.

It provides a centralized location for organizational data and is shared across Fabric workloads.

                Microsoft Fabric
                       │
                    OneLake
                       │
       ┌───────────────┼───────────────┐
       ↓               ↓               ↓
   Lakehouse       Warehouse       Other Data
       │
       ↓
    Spark / SQL
       │
       ↓
    Analytics
## 🏠 Lakehouse

A Fabric Lakehouse combines characteristics of a:

Data Lake
Data Warehouse

It supports both Spark and SQL workloads and uses Delta Lake for table storage.

Lakehouse Architecture
                Lakehouse
                    │
        ┌───────────┴───────────┐
        ↓                       ↓
      Files                   Tables
        │                       │
     Raw Data              Delta Tables
                                │
                       ┌────────┴────────┐
                       ↓                 ↓
                    Spark              SQL
                       │                 │
                       └────────┬────────┘
                                ↓
                            Analytics
## 🥉🥈🥇 Medallion Architecture

A common architecture for organizing data:

Bronze
  ↓
Silver
  ↓
Gold
Bronze

Raw / ingested data.

Silver

Cleaned, validated and transformed data.

Gold

Highly refined data prepared for analytics and reporting.

## 🔧 Important Fabric Components

Component	Purpose
OneLake	Centralized data storage
Lakehouse	Data engineering + analytics storage
Data Factory	Data ingestion and orchestration
Pipelines	Automate data movement
Dataflow Gen2	Low-code data transformation
Notebooks	Code-based data engineering
Apache Spark	Large-scale processing
Warehouse	SQL-based analytics
Power BI	Reporting and visualization

## 💡 Key Takeaways
Microsoft Fabric is an integrated data and analytics platform.
OneLake acts as the central data foundation.
Fabric supports both ETL and ELT approaches.
Lakehouse architecture combines data lake flexibility with warehouse-style analytics.
Spark and SQL can work with Lakehouse data.
Pipelines can be used to orchestrate data movement and transformation.
Medallion architecture organizes data into Bronze, Silver and Gold layers.
