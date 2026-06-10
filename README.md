# Egypt Labor Market Insights Platform
### CAPMAS 2024 Survey Data · End-to-End BI & AI Solution

<div align="center">
  
[![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)](https://powerbi.microsoft.com/)
[![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?style=flat&logo=microsoft-sql-server&logoColor=white)](https://www.microsoft.com/en-us/sql-server)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat&logo=docker&logoColor=white)](https://www.docker.com/)
[![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org/)

<br/>

</div>

---

## 📋 Table of Contents
- [Project Overview](#-project-overview)
- [Problem Statement](#-problem-statement)
- [Solution Architecture](#-solution-architecture)
- [Tech Stack](#-tech-stack)
- [Database Layer (OLTP)](#-database-layer-oltp)
- [Data Warehouse (OLAP)](#-data-warehouse-olap)
- [ETL Pipeline](#-etl-pipeline)
- [Business Intelligence & Reporting](#-business-intelligence--reporting)
- [Power Platform Integration](#-power-platform-integration)
- [AI & RAG System](#-ai--rag-system)
- [Key Features](#-key-features)
- [Screenshots](#-screenshots)
- [My Contribution](#-my-contribution)
- [Acknowledgments](#-acknowledgments)

---

## 🎯 Project Overview

An **end-to-end data platform** built for **CAPMAS Egypt** (Central Agency for Public Mobilization and Statistics) to transform raw labor force survey microdata into actionable intelligence. The platform ingests, models, warehouses, and visualizes **~296,000 individual records** (235 variables each) from the **CAPMAS 2024 National Labor Force Survey**, scaled to represent Egypt's full population of **106 million**.

The solution covers the complete data lifecycle:
&gt; **Raw Data → Cleaning → Relational DB → Data Warehouse → BI Dashboards → AI-Powered Natural Language Queries**

---

## ⚠️ Problem Statement

| Challenge | Impact |
|-----------|--------|
| **Inaccessible Raw Data** | CAPMAS survey data existed in unstructured formats, impossible to analyze at scale |
| **No Unified Insights** | Labor market metrics scattered across static reports with no interactive exploration |
| **Slow Decision-Making** | Policy makers lacked real-time dashboards for employment & economic indicators |
| **No Intelligent Access** | No way to ask natural language questions about the labor market data |

---

## 🏗️ Solution Architecture

---
      
       ─────────────────┐      ┌──────────────┐     ┌─────────────────┐
      │  CAPMAS Survey   ────▶  Data Cleaning ────▶ OLTP Database    │
      │  (Raw CSV)      │      │(Python/Excel)│     │ (SQL Server)    │
      └─────────────────┘      └──────────────┘     └─────────────────┘
                                                            │
                                     ┌──────────────────────┘
                                     ▼
                                ┌─────────────────┐
                                │  Staging Area   │
                                │  (12 Tables)    │
                                └─────────────────┘
                                    │
                                    ▼
                                ┌─────────────────┐
                                │   Data Warehouse│
                                │  (Star Schema)  │
                                │  Fact + 2 Dims  │
                                └─────────────────┘
                                                  │
                                ┌─────────────────┼─────────────────┐
                                ▼                 ▼                 ▼
                                ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
                                │   Power BI   │  │    SSRS      │  │   Tableau    │
                                │  Dashboards  │  │   Reports    │  │  Dashboards  │
                                └──────────────┘  └──────────────┘  └──────────────┘
                                │
                                ▼
                                ┌─────────────────────────────────────────────┐
                                │         AI RAG System (Qdrant + Ollama)     │
                                │    Natural Language Queries → Vector Search │
                                └─────────────────────────────────────────────┘
                                

                                

---

## 🛠️ Tech Stack

| Layer | Technologies |
|-------|-------------|
| **Data Source** | CAPMAS 2024 Labor Force Survey |
| **Data Cleaning** | Python (Pandas), Excel |
| **Database (OLTP)** | SQL Server, Red Gate Tools |
| **Data Warehouse** | SQL Server, Star Schema |
| **ETL** | T-SQL Stored Procedures, SQL Server Agent |
| **BI & Visualization** | Power BI, SSRS, Tableau |
| **Low-Code Apps** | Microsoft Power Apps, Power Automate, SharePoint |
| **Integration** | On-Premises Data Gateway |
| **AI / Vector DB** | Qdrant, Ollama, nomic-embed-text, Llama 3.2 |
| **Orchestration** | n8n, Flask API, Docker |

---

## 🗄️ Database Layer (OLTP)

A fully normalized **SQL Server** relational database (`LabourForceDB`) designed for transactional processing.

### Core Entities
| Table | Purpose |
|-------|---------|
| `Individual` | Demographics (Gender, Age, Marital Status) |
| `Education` | Education level, dropout reasons |
| `Employment` | Job nature, labor force participation |
| `Income` | Basic salary, total monthly earnings |
| `Secondary_Job` | Secondary employment activities |
| `Unemployment` | Unemployment duration, search reasons |
| `Disability` | Difficulty levels (sight, hearing, walking) |
| `Social_Benefits` | Insurance, paid leave coverage |
| `COVID_Impact` | Pandemic effect on work & salary |
| `Governorate` | Geographic hierarchy |
| `Section` | Administrative sub-regions |
| `Survey_Weights` | Statistical weights for national scaling |

### Database Operations
- **51 Stored Procedures** developed for complete CRUD operations
- Cascading deletes on `Individual` to maintain referential integrity
- Analytical procedures for labor market, employment, education & salary analysis
- Parameterized procedures optimized for dynamic BI dashboards

---

## 🏛️ Data Warehouse (OLAP)

A **Star Schema** design optimized for analytical reporting and fast aggregations.

### Schema Design
 ---
  
    ┌──────────────┐   ┌─────────────────┐   ┌─────────────┐
    │  Dim_Location│◄──┤Fact_Individual_ │──►│   Dim_Date  │
    │ (Gov, Section│   │    Survey       │   │(Time Intel) │
    └──────────────┘   └─────────────────┘   └─────────────┘
---

### Fact Table: `Fact_Individual_Survey`
Contains all measurable survey metrics:
- Demographics, Education, Employment Status
- Income & Salary, Disability, Social Benefits
- COVID Impact, Survey Weights

### Dimension Tables
| Dimension | Attributes |
|-----------|-----------|
| **Dim_Location** | `Location_Key`, `Gov_ID`, `Governorate_Name`, `Section_Name`, `Household_Region_Type` |
| **Dim_Date** | `Date_Key`, `Quarter_ID`, `Quarter_Label`, `Quarter_Start_Date`, `Quarter_End_Date`, `Year` |

**Design Principles:**
- Surrogate keys on all dimensions
- Denormalized for fast aggregation
- Optimized for Power BI DirectQuery
- No cross-dimensional joins needed

---

## 🔄 ETL Pipeline

### Phase 1: Full Load (Initial Population)
- **296,000 records** loaded from 12 source tables
- One-time safe transfer: OLTP → Staging → Transform → DWH
- Data isolation: DWH never reads directly from production OLTP

### Phase 2: Incremental Load (Production)
Built for scalability using **Audit Columns + ETL Control Framework**:

| Component | Implementation |
|-----------|---------------|
| **Audit Columns** | `CreatedDate`, `ModifiedDate` on every staging table |
| **ETL_Control Table** | Tracks `Last_Load_Date` per source table |
| **Staging SPs** | 4 procedures extract only new records (`WHERE CreatedDate > Last_Load_Date`) |
| **Dim Load** | `NOT EXISTS` pattern preserves surrogate keys |
| **Fact Load** | `MERGE` statement (Upsert): `MATCHED → UPDATE`, `NOT MATCHED → INSERT` |
| **Automation** | SQL Server Agent Job runs the full pipeline automatically |

---

### Data Quality Safeguards

    Layer 1: Incremental Load (CreatedDate filter)
    Layer 2: MERGE Statement (WHEN MATCHED)
    Layer 3: Unique Index on Fact Table (DB-level enforcement)
    ─────────────────────────────────────────
    Result: 0 Duplicate Records | 100% Upsert Accuracy


---

## 📊 Business Intelligence & Reporting

### Power BI Dashboards (17 Dashboards · 5 Thematic Groups)

| Group | Dashboards | Key Insights |
|-------|-----------|-------------|
| **Employment & Labor Market** (4) | Employment Status, Workforce Trends, Education vs Employment, Unemployment | Labor force KPIs, participation rates |
| **Income & Economic Profile** (4) | Income Profile, Inequality, Household Economic, Secondary Jobs | Salary distribution, gender gaps, sector analysis |
| **Education & Training** (3) | Educational Profile, Training & Dropout, Job Quality | Literacy rates, dropout reasons, training impact |
| **Demographics & Geography** (3) | Population Overview, Geographic Distribution, Disability & Social Benefits | Regional disparities, disability statistics |
| **Social Protection & Safety Net** (2) | Social Benefits, COVID-19 Economic Impact | Insurance coverage, pandemic effects |

**Key Dashboards Built:**
1. **Educational Profile of the Egyptian Workforce** — Literacy rates, education distribution by gender, dropout analysis, quarterly trends
2. **Employment Status Overview** — Workers count, labor force size, informal workers, employment by age group
3. **Income and Salary Analysis** — Salary by governorate/sector/education, gender salary gap (Urban vs Rural)
4. **Professional Training & Dropout Analysis** — Training participation, training-to-employment rate, dropout reasons by gender
5. **Social Benefits & Safety Net** — Social/health insurance coverage, paid leave, regional gaps

### SSRS Reports (6 Reports)
Parameterized reports for paginated distribution:
- `sp_Employment_ByEducationLevel` — Labor force KPIs & salaries by education
- `sp_Income_SalaryDistribution` — Min, max, avg salaries by demographics
- `sp_LaborForce_StatsByGovernorate` — Employment/unemployment by governorate
- `sp_Disability_Select` — Disability difficulty levels
- `sp_Employment_Select` — Employment details & contract types
- `sp_Income_Select` — Basic salary & overtime tracking

**Featured Report:** *The Impact of Coronavirus on Salaries and Working Hours* — Analyzing average salary reduction (29-30%) across different COVID-19 impact scenarios.

### Tableau Dashboards (3 Dashboards)
1. **Survey Coverage & Representativeness** — Data quality monitoring across governorates
2. **Social Vulnerability Index** — Risk factor analysis by education, region type, and age
3. **Income and Employment Statement** — Wage architecture across public, private, and informal sectors

---

## ☁️ Power Platform Integration

A complete **low-code data entry and integration** solution connecting field survey operations to the central database.

### Architecture Flow

---
      Power Apps (Mobile/Tablet)
          │
          ▼
      SharePoint List (Cloud Storage Layer)
          │
          ▼
      On-Premises Data Gateway
          │
          ▼
      SQL Server Database
---

### Components

| Component | Details |
|-----------|---------|
| **Power Apps** | 3-screen canvas app: Home Screen → Submit Screen → Success Screen |
| **SharePoint List** | `Labor_Force` list acts as cloud buffer and intermediate storage |
| **Power Automate** | Flow: `SharePoint_To_SQL` — triggers on new item creation |
| **Data Gateway** | `LaborForceGateway` (North Europe) — bridges cloud to on-premises SQL Server |

### Challenge & Resolution
- **Problem:** University tenant DLP policy blocked direct SharePoint → SQL Server data transfer
- **Solution:** Deployed **On-Premises Data Gateway** to establish secure, policy-compliant connectivity between Power Platform and local SQL Server

### Verified End-to-End Pipeline
- Data submitted via Power Apps → Stored in SharePoint → Inserted into SQL Server via Gateway
- **SSMS Verification:** `SELECT TOP 1000` confirmed successful data insertion with all survey fields properly stored

---

## 🤖 AI & RAG System

A **Retrieval-Augmented Generation (RAG)** system enabling natural language queries on labor market data.

### Architecture      

User Query → Flask API (Port 5000) → n8n Workflow
│
├── Statistical Request → Direct computation (counts, averages)
├── Analytical Request → Comparison, trends, insights
└── Individual Request → Semantic search for specific records


### Tech Components
| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Vector Database** | Qdrant | Stores ~296,000 records as 768-dimensional vectors |
| **Embeddings** | nomic-embed-text | Converts text to vector embeddings |
| **LLM** | Llama 3.2 (via Ollama) | Generates natural language responses |
| **Similarity** | Cosine Similarity | Semantic search (not just keyword matching) |
| **Orchestration** | n8n | Automated ingestion and chat workflows |
| **Infrastructure** | Docker | Fully containerized, offline-capable deployment |

### How It Works
1. User asks a question in natural language (Arabic/English)
2. Query converted to vector embedding (`nomic-embed-text`)
3. Qdrant retrieves semantically similar records
4. Llama 3.2 generates contextual response based on retrieved data
5. Response returned to user via webhook

**Example:** Querying *"employment rate"* retrieves documents about *"job statistics"* or *"labor force participation"* even without exact keyword matches.

---

## ✨ Key Features

- ✅ **End-to-End Pipeline** — From raw census microdata to AI-powered insights
- ✅ **Scalable ETL** — Incremental load with 0 duplicates and 100% upsert accuracy
- ✅ **Multi-Engine BI** — Power BI, SSRS, and Tableau covering all reporting needs
- ✅ **Low-Code Integration** — Power Apps for field data collection with direct DB insertion
- ✅ **Offline AI** — Fully local RAG system using Docker + Ollama (no external API costs)
- ✅ **Semantic Search** — Vector-based retrieval understanding meaning, not just keywords
- ✅ **National Scale** — 296K records weighted to represent 106M Egyptians

---

## 📸 Screenshots

| Dashboard / Component | Preview |
|----------------------|---------|
| **Educational Profile** | `education profile.png` |
| **Employment Overview** | `employment status overview.png` |
| **Income Analysis** | `income and salary analysis.png` |
| **Training & Dropout** | `profitional training.png` |
| **Social Benefits** | `social benefits.png` |
| **SSRS COVID Report** | `SSRS Report.png` |
| **Star Schema Model** | `star chema model.png` |
| **Power Apps Home** | `Home Screen.png` |
| **Power Apps Submit** | `Submit Screen.png` |
| **Power Apps Success** | `Success Screen.png` |
| **SharePoint List** | `SharePoint List.png` |
| **SQL Server (SSMS)** | `ssms.png` |
| **On-Premises Gateway** | `Gateway.png` |
| **Power Automate Flow** | `Power Automate.png` |
| **DLP Challenge** | `Power Automate Challenge.png` |

---

## 👤 My Contribution

I was responsible for the **complete backend analytics and integration architecture** of this project:

### 🔹 Data Warehouse (Solo Development)
- Designed and implemented the **Star Schema** (`Fact_Individual_Survey`, `Dim_Location`, `Dim_Date`)
- Built the full **ETL pipeline** from scratch:
  - Staging area with 12 tables
  - Full load procedure for initial 296K record population
  - Incremental load framework with audit columns and `ETL_Control` table
  - `MERGE`-based fact table loading with duplicate prevention
  - SQL Server Agent Jobs for full automation
- Implemented **3-layer data quality defense** (Incremental → MERGE → Unique Index)

### 🔹 Business Intelligence (Solo Development)
- Built **5 Power BI Dashboards** covering Education, Employment, Income, Training, and Social Benefits
- Developed **1 SSRS Report** analyzing COVID-19 impact on salaries and working hours
- Created parameterized stored procedures to power dynamic BI visualizations

### 🔹 Power Platform Integration (Solo Development)
- Built the **Power Apps** 3-screen survey application from scratch
- Configured **SharePoint List** as the cloud data buffer
- Developed **Power Automate Flow** (`SharePoint_To_SQL`)
- Installed and configured **On-Premises Data Gateway** to resolve DLP policy restrictions
- Verified end-to-end data flow: Power Apps → SharePoint → Gateway → SQL Server

---

## 🙏 Acknowledgments

- **CAPMAS Egypt** — For providing the 2024 National Labor Force Survey dataset
- **Information Technology Institute (ITI)** — For academic guidance and project framework
- **Project Team** — For collaboration on the OLTP database design and ERD modeling

---

> **"From raw data to actionable labor market intelligence — powered by CAPMAS 2024"**
